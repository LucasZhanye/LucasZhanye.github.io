# Kratos参数验证实践

开发Web应用，参数验证能够帮我们卡掉一些不合法的请求，校验参数的正确与否，能拦截掉无效请求，从而防止错误请求打到后端服务上。

本文介绍使用微服务框架Kratos如何相对舒服一点的做参数验证。

### 以前的方式
刚开始接触kratos时，二话不说，直接简单粗暴，就在service层，每一个接口方法里去做参数验证，比如：
```go
func (s *UserService) Login(ctx context.Context, req *v1.LoginReq) (*v1.LoginResp, error) {
	if req.name != "" {
		return nil, errors.New("name 不可为空")
	}
	// ....
    resp, err := s.uc.Login(ctx, req)

    if err != nil {

        s.log.Errorf("Login fail: %v", err)

        return nil, errors.Unwrap(err)

    }
    return resp, nil
}
```

这种方式太费劲了，并且每一个都要写（由于每个接口的参数验证都不完全相同），太不优雅了。

### 现在的方式
以前的方式不够优雅，主要有几个问题：
1. 参数验证全部自己写
2. 每一个接口都要写参数验证相关代码
对于这两个问题，有以下解决方法：
1. 在golang的领域，已经有比较成熟的参数校验相关的第三方包[validator](https://github.com/go-playground/validator)了，可以直接拿来用，这样可以不需要所有参数验证都自己写，能简化很多代码量
2. 每个接口都会需要参数验证的情况，可以利用中间件来处理

再考虑到kratos是使用proto来定义接口以及请求参数的，可以结合第三方工具[protoc-go-inject-tag](https://github.com/favadi/protoc-go-inject-tag)，实现在proto中定义参数时，定义好参数的校验规则。

### 说干就干
#### 自定义中间件
首先，先在Kratos中自定义一个中间件，用来处理参数验证，在`internal/pkg/middleware`中新建文件`validator.go`
```go
package middleware

import (
	"context"
	"mytest/cloudapis/mytest"
	"net/http"
	"sync"

	"github.com/go-kratos/kratos/v2/errors"

	"github.com/go-kratos/kratos/v2/middleware"
	"github.com/go-playground/validator/v10"

	"github.com/go-playground/locales/zh"
	ut "github.com/go-playground/universal-translator"
	zhTrans "github.com/go-playground/validator/v10/translations/zh"
)

var (
	gValidate *validator.Validate
	trans     ut.Translator
	once      sync.Once
)

// CustomValidator 自定义验证器的结构体
type CustomValidator struct {
	Field      string
	Msg        string
	ValidateFn validator.Func
}

// RegisterCustomValidators 注册自定义验证器
func registerCustomValidators(validate *validator.Validate, trans ut.Translator, customValidators []CustomValidator) error {
	for _, cv := range customValidators {
		// 注册翻译器
		if cv.Msg != "" {
			if err := validate.RegisterTranslation(
				cv.Field, // 字段
				trans,    // 翻译对象
				registerTranslator(cv.Field, "{0}"+cv.Msg),
				translate, // 翻译的方法
			); err != nil {

			}
		}
		validate.RegisterValidation(cv.Field, cv.ValidateFn)
	}
	return nil
}

func InitValidator(customValidators ...CustomValidator) {
	once.Do(func() {
		// Validate/v10 中文配置
		zh := zh.New()
		uni := ut.New(zh, zh)
		trans, _ = uni.GetTranslator("zh")

		// 初始化Validate/V10
		gValidate = validator.New()

        // 注册一个获取json tag的自定义方法
		gValidate.RegisterTagNameFunc(func(fld reflect.StructField) string {
			name := strings.SplitN(fld.Tag.Get("json"), ",", 2)[0]
			if name == "-" {
				return ""
			}
			return name
		})
        
		// 注册默认的翻译器
		err := zhTrans.RegisterDefaultTranslations(gValidate, trans)
		if err != nil {
			panic("Validate/v10 international error: " + err.Error())
		}

		// 注册自定义验证器
		err = registerCustomValidators(gValidate, trans, customValidators)
		if err != nil {
			panic("Failed to register custom validators: " + err.Error())
		}
	})
}

// registerTranslator 为自定义字段添加国际化功能
func registerTranslator(tag string, msg string) validator.RegisterTranslationsFunc {
	return func(trans ut.Translator) error {
		if err := trans.Add(tag, msg, false); err != nil {
			return err
		}
		return nil
	}
}

// translate 自定义字段的翻译方法
func translate(trans ut.Translator, fe validator.FieldError) string {
	msg, err := trans.T(fe.Tag(), fe.Field())
	if err != nil {
		panic(fe.(error).Error())
	}
	return msg
}

// Validate 参数校验中间件
func Validate() middleware.Middleware {
	return func(handler middleware.Handler) middleware.Handler {
		return func(ctx context.Context, req interface{}) (interface{}, error) {
			// do something entering
			err := gValidate.Struct(req)
			if err != nil {
				errs := err.(validator.ValidationErrors)
				return nil, errors.New(http.StatusBadRequest, mytest.ErrorReason_ERROR_REASON_PARAMS.String(), errs[0].Translate(trans))
			}

			defer func() {
				// do something exiting
			}()

			return handler(ctx, req)
		}
	}
}
```
从上述代码中，我们定义了一个`InitValidator`，他接收一个CustomValidator类型切片，这个就是自定义校验规则的结构，支持我们自己定义一些特殊的校验规则：
```go
type CustomValidator struct {
	Field      string
	Msg        string
	ValidateFn validator.Func
}
```
我们只需要实现一个ValidateFn,然后将这个struct在初始化时丢进去，就能够支持我们的自定义的校验规则

#### 自定义校验规则
只需要定义一个ValidateFn方法，比如：
```go
func ValidateQuarter(f1 validator.FieldLevel) bool {
	field := f1.Field().String()

	regexPattern := `^Q[1-4]'[0-9]{2}$`

	// 使用正则表达式进行匹配
	matched, err := regexp.MatchString(regexPattern, field)
	if err != nil {
		return false
	}

	return matched
}
```
之后再丢进`InitValidator`:
```go
localMiddleware.InitValidator(localMiddleware.CustomValidator{
    Field: "quarter",
    Msg: "季度格式不正确",
    ValidateFn: custom.ValidateQuarter,
})
```
#### 注册中间件
定义好中间件后，在http.go和grpc.go中注册这个中间件:
![image.png](https://raw.githubusercontent.com/LucasZhanye/MyNotePic/master/NoteImg/202405152027554-20240515-yhcid6DDwT.png)

#### proto中定义规则
在proto文件中可以这样使用，通过`@gotags: validate:"required"`的形式设置参数规则，如下：
```go
message QueryOEEInfoReq {
  // @gotags: validate:"required"
  string name = 1;
  // @gotags: validate:"required"
  string address = 2;
  // @gotags: validate:"quarter"
  string id = 3;
  // @gotags: validate:"quarter"
  string quarter = 4;
}
```
> 已经添加了自定义校验规则的，定义的字段就与`validator`包默认的字段一样，直接在proto中使用即可

在proto中使用，有几点需要注意：
1. 如果要嵌套校验，比如一个结构嵌套另一个结构，则需要使用`dive`标注，比如：
```go
message Data {
  // ID 值 后端生成
  string id = 1;
  // @gotags: validate:"required"
  string type = 2;
  // @gotags: validate:"quarter"
  string quarter = 3;
}
message CreateEQTypeReq {
  // @gotags: validate:"required"
  string id = 1;
  // @gotags: validate:"required"
  string name = 2;
  //@gotags: validate:"required,dive,required"
  repeated Data data = 3;
}
```
如此一来，data字段里的quarter字段也会校验到。
2. 编译处理， proto编译需要新增：`protoc-go-inject-tag -input=./cloudapis/${NAME}/*/*.pb.go` 


