# Hugo与Obsidian搭配食用

## 1. 前言

上一篇简单记录了Hugo搭建个人博客，最后总结了发布一篇文章至少需要这几个步骤：
1. 本地生成博客文件(xxx.md)，编辑
2. 本地运行看看效果，记得将draft改为true
3. 执行hugo命令进行编译
4. 进入public目录，执行git命令上传:
	1. `git add .` 
	2. `git commit -m "xxxx"`
	3. `git push`
5. 查看网站
其中，步骤比较麻烦的就是第4步了，每一次修改或新增文章后，都需要重新部署，上传git，实在是繁琐！
## 2. 解决思路
针对上述流程的第1,2步是必不可少的，第2步其实可以省略，修改draft也可以放在第1步，也就是说我们要解决的其实是如何自动编译，并且自动上传git进行部署。

我的思路是这样的：创建两个Github仓库，分别存储笔记的源文件以及博客站点的编译产物public目录，使用Github Action工作流，在完成文章要发布时，将文章更新到源文件仓库，然后Github的工作流会自动帮我们构建编译生成Hugo的public目录，并且自动推送到public目录所在的仓库，如此一来，就可以省了编译部署这一繁琐流程了。
### 2.1 如何自动编译？
要自动编译，我们可以使用Github Actions在仓库有更新时自动进行编译，这个是最简单实现的
> github action可以了解这里[了解 GitHub Actions - GitHub 文档](https://docs.github.com/zh/actions/learn-github-actions/understanding-github-actions)

操作流程如下：
1. 创建两个仓库，一个为私有的，比如MyNote，用来存放笔记源文件，同时也可以作为一个备份，另一个为公开的，仓库名必须为**username.github.io** ,这里的username指的是你github账号名
2. 在MyNote仓库里根目录新建`.github/workflows`目录，然后在里面创建一个`main.yaml`
3. 编辑`main.yaml`文件，设置Github Action配置，我的是这样的：
```yaml fold
name: deploy

on:
    push:
    workflow_dispatch:

jobs:
    build:
        runs-on: ubuntu-latest
        steps:
            - name: Checkout
              uses: actions/checkout@v2
              with:
                  submodules: true
                  fetch-depth: 0

            - name: Setup Hugo
              uses: peaceiris/actions-hugo@v2
              with:
                  hugo-version: "0.124.1"
                  extended: true
                  # 这里最好与本机hugo版本一致，例如：hugo-version: "0.124.1"
                  # 有时github部署会自动更新hugo版本导致部署失败
                  # extended来控制是否为扩展版本

            - name: Build Web
              run: hugo

            - name: Deploy Web
              uses: peaceiris/actions-gh-pages@v3
              with:
                  personal_token: ${{ secrets.PERSONAL_TOKEN }}
                  external_repository: LucasZhanye/LucasZhanye.github.io
                  publish_branch: master
                  publish_dir: ./public
                  commit_message: ${{ github.event.head_commit.message }}

```
```ad-tip
1. 这里你需要关心的有两个：`personal_token`和`external_repository`,其他的基本上照搬即可。
2. `external_repository`指的是你要部署的仓库，这里肯定是我们另一个存放hugo的public目录的仓库了
3. 当然这里hugo的版本尽量与你本机的一样
4. `publish_branch`,如果你的是main分支，那就改为main
```
4. 这里有一个`personal_token`，需要在自己的仓库里创建一番，具体如下操作：
	1. 在个人github账户下，点击`Settings` -> `Developer Settings` -> `Personal access tokens` -> `Tokens(classic)`,然后点击`Generate new token`
	 ![image.png](https://raw.githubusercontent.com/LucasZhanye/MyNotePic/master/NoteImg/202404302236125-20240430-AsjX2EsQgP.png)
	 2. 给Token起个名字，选择有效期为`No Expiration`，**勾选repo以及workflow`,并且repo是要全选**
	  ![image.png](https://raw.githubusercontent.com/LucasZhanye/MyNotePic/master/NoteImg/202404302239763-20240430-7Z93slRl4L.png)
```ad-caution
title: 注意
注意创建成功后，这个Token先保存起来，只会出现一次的
```
5. 创建完Token后，回到博客源仓库，给他设置token，点击仓库的`Settings`->`Secrets and variables` -> `Actions`,在`Secrets`栏中点击`New repository secret`,填入Secret Name和Secret
> 1. 这里填入的SecretName随便指定，但是这里设置了什么，上面的main.yaml的`personal_token`的值就是对应的名字。
> 2. 将第四步创建的Token填入这里的Secret栏处
> 
### 2.2 实操步骤
完成上述配置后，将源文件仓库代码push到Github上，就会自动执行我们设置好的workflow，然后构建好Hugo站点，这时打开网页访问:xxxx.github.io就可以看到我们的博客站点了。

## 3. 寻找合适的编辑器
到了这里，Hugo的博客站点已然完成，其实已经可以结束了，但是既然涉及到写作，那选择一款好用的Markdown编辑器肯定会用着很舒服，基于此原因，又去调研了一下当前比较火的笔记软件，最终发现了Obsidian这一款宝藏软件，感觉与他相见恨晚...

之所以选择了Obsidian，很大一个原因是因为他支持本地存储，结合之前搭建Hugo中创建的源文件仓库，就很适合将笔记或博客备份到Github了。

另一个原因是因为他的插件很丰富，真的可以做到很实用并且颜值高~,当然还有其他的许多好玩的，以后慢慢摸索。

既然选择好了编辑器Obsidian，那接下来就是让Hugo与Obsidian搭配起来，让整套流程更加丝滑，做到“all in Obsidian”，也就是我们只需要在Obsidian中编写笔记或博客即可，其他所有事情都不用再操心。

不了解Obsidian的，可以先下载了解使用一番，以下是我个人的一些操作配置。

### 3.1 Obsidian创建博客文章
Obsidian的插件是非常的丰富的，而且Obsidian是以一个文件夹作为一个仓库的，这一点很完美的适合Hugo的站点，我们可以选择将Hugo的站点目录作为Obsidian的一个仓库，只需要在打开Obsidian之后，选择好这个目录打开就可以（在站点目录下会生成一个.obsidian的目录）

回忆一下，在使用Hugo时，创建文章，我们需要使用命令：`hugo new posts/xxx.md`，就会帮我们在content目录下的posts文件夹创建一个md文件，并且自动生成了一些文档属性。

这一个步骤是需要使用终端控制台去敲命令的，而到了Obsidian这里，大可不必，插件用用用...

为了实现这一个优化，推荐两个插件:`QuickAdd`和`Templater`，还有`Commander`
`QuickAdd`:是一个可以添加快速命令的插件
`Templater`：是一个创建模板的插件
`Commander`: 是一个可以让我们在侧边栏等地方创建自定义的命令按钮

利用这三个，我们可以将创建文章这一个集成到Obsidian里实现，具体如下操作：
1. 首先在站点目录下，创建一个`custom`目录，再在里面创建两个目录：`scripts`和`templates`
2. 在`templates`目录下创建一个模板文件,比如`新建博客模板.md`
```markdown
---
title: "{{VALUE:articleTitle}}"
subtitle: ""
date: {{VALUE:articleTimestamp}}
categories: [{{VALUE:articleCategory}}]
tags: [{{VALUE:articleTag}}]
toc: 
    enable: true
    auto: true
draft: true
---
```
> 这个模板文件定义的是生成的文章的文档属性，里面包含了一些必要的，也可以自行添加。
3. 在`scripts`下创建脚本文件，比如`新建博客脚本.js`
```js
module.exports = async (params) => {
    QuickAdd = params;

    const title = await QuickAdd.quickAddApi.inputPrompt("文章标题 - Title");
    var tags = await QuickAdd.quickAddApi.inputPrompt("文章标签 - Tags");
    const categorys = await QuickAdd.quickAddApi.inputPrompt("文章类别 - Categories");

    let titleName = title.split("/"); // 将路径字符串拆分成数组
    let name = titleName[titleName.length - 1]; // 获取数组的最后一个元素
    
    QuickAdd.variables["articleTitle"] = name;
    QuickAdd.variables["articleFilename"] = title
    QuickAdd.variables["articleCategory"] = categorys;
    QuickAdd.variables["articleTag"] = tags;
    QuickAdd.variables["articleTimestamp"] = QuickAdd.quickAddApi.date.now('YYYY-MM-DDTHH:mm:ssZ');
};
```
> 这个脚本实质上是用来给模板文件的参数设置值的，这里的title，tag等通过输入提示框来获取，获取到的值会替换掉模板文件中的参数
4. 接下来，在Obsidian中，找到`QuickAdd`设置页面，点击`Manage Macros`,输入一个名字，比如"新建文章",然后点击`add macros`
	![image.png](https://raw.githubusercontent.com/LucasZhanye/MyNotePic/master/NoteImg/202404302335485-20240430-D4EXCmiWMu.png)
	![image.png](https://raw.githubusercontent.com/LucasZhanye/MyNotePic/master/NoteImg/202404302337434-20240430-zUhOR6894X.png)
5. 点击`Configure`,选择脚本，然后点击添加
	![image.png](https://raw.githubusercontent.com/LucasZhanye/MyNotePic/master/NoteImg/202404302339024-20240430-CY33Pm0wUe.png)
6. 之后再点击`Template`，然后点击配置(小齿轮)
	![image.png](https://raw.githubusercontent.com/LucasZhanye/MyNotePic/master/NoteImg/202404302340417-20240430-CFeo3eK6FW.png)
	![image.png](https://raw.githubusercontent.com/LucasZhanye/MyNotePic/master/NoteImg/202404302341068-20240430-LrBKgnRfAk.png)
7. 配置如下：这里需要注意的是选择好保存的文件夹后要记得点击Add，否则是不会生效的。这里配置了文件名跟文章Title一样
	![image.png](https://raw.githubusercontent.com/LucasZhanye/MyNotePic/master/NoteImg/202404302343423-20240430-bp7DPoKkQW.png)
8. 然后回到最初的配置页面，新建一个Marco，注意要选择**Macro**
	![image.png](https://raw.githubusercontent.com/LucasZhanye/MyNotePic/master/NoteImg/202404302345885-20240430-S1TRpjTOuO.png)
	之后就点亮这个小闪电!
	![image.png](https://raw.githubusercontent.com/LucasZhanye/MyNotePic/master/NoteImg/202404302346742-20240430-waajhXJta6.png)
9. 到这里已经创建好了，可以使用`ctrl+p`调出命令窗口，输入`QuickAdd`关键字，就可以看到我们新创建的命令了
	![image.png](https://raw.githubusercontent.com/LucasZhanye/MyNotePic/master/NoteImg/202404302347877-20240430-hL5VbCdzNR.png)
	点击这个命令，就会出现对话框，让我们输入文件Title等信息，一路输入即可，最终就会创建出来一个文章
10. 最后，我们再利用`Commander`插件，在侧边栏给我们新增一个命令按钮，同样的操作，打开`Commander`配置页面，选择要将按钮放在哪个位置，然后添加命令即可。
	![image.png](https://raw.githubusercontent.com/LucasZhanye/MyNotePic/master/NoteImg/202404302350541-20240430-Z6uWh5v38F.png)
	比如我的是选择在左侧边栏，那么就会在左侧边栏多一个按钮，点击就是创建文章的流程了，还是十分舒服的
	![image.png](https://raw.githubusercontent.com/LucasZhanye/MyNotePic/master/NoteImg/202404302351776-20240430-DkA70BHWPv.png)

### 3.2 Obsidian Git操作
上一步已经实现了在Obsidian中去创建博客文章了，这样一来不需要再使用终端去新增，对于“all in Obsidian”更进一步了，仔细想想，目前好像就差一个git push博客文章到源文件仓库这一个操作脱离在Obsidian之外了，那么接下来就来解决掉这个问题。

Obsidian插件是非常的丰富的，其中就有一款是跟Git有关的，叫`Obsidian Git`,后来又改名为`Git`，直接在第三方插件库搜索`Git`即可找到。

因为我们这个仓库本身就是源文件仓库，是存放在Github上的，因此这个目录下有`.git`目录，Git插件就会识别到，我们只需要配置好Git插件就可以实现自动备份，上传文件了，比如设置每30分钟自动提交push，设置commit message格式等。

同样也可以使用上面提到的插件搞个命令按钮，手动提交，方便我们想要实时发布的情况。

### 3.3 处理图片问题
通过上面两步，我们已经可以做到了只需要打开Obsidian进行写作即可，其他一概都不用关心，要发布的文章，只需要编辑完成后将文档属性的`draft`改为true，然后Git会自动备份上传，上传完成后又会自动构建发布。

实现了“all in obsidian”后，我们还有一个问题可以优化处理，那就是图片资源的存放问题，一般有两种处理方法：
1. 与文章放一起，比如创建一个img目录专门存放文章用到的图片，通过相对路径引用，在上传Github时随文章一起上传
2. 使用图床存储，然后通过外链进行引用

其实两种方案都可以，各有各的好处，但是我个人倾向推荐方案2

先说方案1，这里可以使用一个Obsidian的插件：`Custom Attachment location` 这个插件可以让我们类似Typora一样指定一个目录作为附件存放路径，当我们复制图片进文章时会自动在文章所在路径下指定的目录中保存这个图片，具体路径要自己配置下就可以。

方案2，推荐使用一款工具，叫做[PicList](https://github.com/Kuingsmile/PicList/blob/dev/README_cn.md)，需要先下载到本地

PicList是一款高效的云存储和图床平台管理工具，在PicGo的基础上经过深度的二次开发，不仅完整保留了PicGo的所有功能，还增添了许多新的feature。

然后Obsidian中有一个插件`Image auto upload Plugin`支持这个`PicList`，通过配置后，可以实现在文章中复制图片时能够自动上传到我们的图床，用起来十分丝滑。

>PS: 这里我使用了Github作为图床，穷鬼没法子~

具体的配置很简单的，可以看看PicList的说明就能上手了，同时打开PicList软件页面也可以对自己的图床进行管理，可以删除图片，增加水印，压缩等等操作

### 3.4 处理链接问题
当我写完第二篇，在第一篇上将第二篇文章的链接给加上后，在hugo渲染后，页面点击是一个不存在的页面，然后我看了下生成的url路径好像有点不对，然后一搜索发现有牛人已经给了方案去处理hugo渲染链接的问题了，使用了Hugo提供的render hook机制。
参考这篇：[用 hugo render hooks 简化 markdown 中链接、图片的引用](https://juejin.cn/post/7353456468094369829)

至此，一切就准备就绪了，可以开干了...

## 4. 小结
这一篇主要是针对Hugo发布博客这一繁琐流程做一个优化，既然选择了Obsidian作为笔记软件，那么日常的文章，笔记自然而然是使用Obsidian来编写记录了，经过摸索，发现了Obsidian的一些插件很适配Hugo发布博客的流程优化，因此将hugo与Obsidian搭配起来一起使用，做到“all in obsidian”，就是只需要使用Obsidian写笔记文章即可，不需要额外再做一系列操作。
