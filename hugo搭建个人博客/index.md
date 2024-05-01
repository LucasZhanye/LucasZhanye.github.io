# Hugo搭建个人博客

很长一段时间没有整博客，也没时间写博客，最近又重新捣鼓上了，还是觉得得自己搭一个，这次选择的是Hugo这个静态站点生成器，结合Github就能搭建出属于自己的一个博客平台了。

具体Hugo的搭建方式，网上应该有一堆的教程了，我这里就只做一些简单的记录，更细节的还得深入探索才行。（其实还是蛮适合小白的，主要是流程清晰）

### 1. 安装
直接看官方文档[Windows | Hugo官方文档](https://hugo.opendocs.io/installation/windows/)

安装完成后，可以做个测试确认是否安装成功，一般是获取下版本号：
```shell
>hugo version
hugo v0.124.1-db083b05f16c945fec04f745f0ca8640560cf1ec+extended windows/amd64 BuildDate=2024-03-20T11:40:10Z VendorInfo=gohugoio
```
看到以上输出信息就表示hugo已经安装成功了。

### 2. 创建站点
在终端输入命令：`hugo new site Test`，就会生成一个Test的目录，里面的子目录就是Hugo创建的，用于站点内容，结构，行为和呈现的,具体每个目录的作用干嘛的，还是直接去看官方文档。

这里要说下的是hugo.toml是整个站点的配置文件，接下来的配置也主要是针对它。

### 3. 选择主题
hugo启动站点前，需要先配置好主题（否则你会得到一个**Page Not Found**页面），这里推荐一款`Loveit`，网上用的人也挺多，我自己本身使用的也是这个，照例甩上官方文档[主题文档 - 基本概念 - LoveIt](https://hugoloveit.com/zh-cn/theme-documentation-basics/)

可以从Github上直接下载这个主题的源码，放入`themes`这个目录，然后在`hugo.toml`中配置上`theme = "LoveIt"`就可以了。

接下来就可以本地启动站点进行预览了，进入到站点目录中，还是使用终端敲命令：
`hugo serve -D`
然后访问本地的:`http://127.0.0.1:1313`,就可以看到你的站点了，当然目前的站点空空如也，啥也没有。

你可以创建一篇文章，使用命令:`hugo new posts/test.md`,这样就会自动在子目录content下创建一个目录posts，并且创建了一个test.md文件，编辑test.md文件，就是你的博客文件。

```ad-note
title: 提示

1. 首先，你要想这个博客能在网页站点上显示出来，你需要将头部的`draft`设置为true，表示非草稿
2. 然后你需要重新启动下命令`hugo server -D`,好像也可以不用，只需要刷新网页就可以
```
然后你就可以看到如下图：
![image.png](https://raw.githubusercontent.com/LucasZhanye/MyNotePic/master/NoteImg/202404251951104-20240425-K5QcZtWSQP.png)


初始界面如上图，是不是很简陋，那是因为我们还没加上一些主题的配置。

### 4.配置
既然选择了主题`Loveit`，那想要漂亮肯定是要按要求配置的，不懂的怎么配置可以参考这个主题自带的一个example，路径在:`themes/LoveIt/exampleSite/config.toml`

简单粗暴，可以将这个文件内容直接复制，然后粘贴到站点根目录下的`hugo.toml`，然后根据显示的页面再去具体调配。

这里放上我用的配置：
```toml fold
baseURL = "https://LucasZhanye.github.io/"

# 更改使用 Hugo 构建网站时使用的默认主题
theme = "LoveIt"

# 网站标题
title = "LucasZhang个人博客"
defaultContentLanguage = "zh-cn"
# 网站语言, 仅在这里 CN 大写 ["en", "zh-CN", "fr", "pl", ...]
languageCode = "zh-CN"
# 语言名称 ["English", "简体中文", "Français", "Polski", ...]
languageName = "简体中文"

# 是否包括中日韩文字
hasCJKLanguage = true

# 默认每页列表显示的文章数目
paginate = 12
# 谷歌分析代号 [UA-XXXXXXXX-X]
googleAnalytics = ""
# 版权描述，仅仅用于 SEO
copyright = ""

# 是否使用 robots.txt
enableRobotsTXT = true
# 是否使用 git 信息
#enableGitInfo = true
# 是否使用 emoji 代码
enableEmoji = true

# 忽略一些构建错误
ignoreErrors = ["error-remote-getjson", "error-missing-instagram-accesstoken"]

# 作者配置
[author]
  name = "LucasZhang"
  email = ""

# 菜单配置
[menu]
  [[menu.main]]
    weight = 1
    identifier = "home"
    # 你可以在名称 (允许 HTML 格式) 之前添加其他信息, 例如图标
    pre = "<i class='fa-solid fa-house'></i>"
    # 你可以在名称 (允许 HTML 格式) 之后添加其他信息, 例如图标
    post = ""
    name = "主页"
    url = "/"
    # 当你将鼠标悬停在此菜单链接上时, 将显示的标题
    title = ""
  [[menu.main]]
    weight = 2
    identifier = "posts"
    # 你可以在名称 (允许 HTML 格式) 之前添加其他信息, 例如图标
    pre = "<i class='fa-brands fa-markdown'></i>"
    # 你可以在名称 (允许 HTML 格式) 之后添加其他信息, 例如图标
    post = ""
    name = "文章"
    url = "/posts/"
    # 当你将鼠标悬停在此菜单链接上时, 将显示的标题
    title = ""
  [[menu.main]]
    weight = 3
    identifier = "tags"
    pre = "<i class='fa-solid fa-tags'></i>"
    post = ""
    name = "标签"
    url = "/tags/"
    title = ""
  [[menu.main]]
    weight = 4
    identifier = "categories"
    pre = "<i class='fa-solid fa-list'></i>"
    post = ""
    name = "分类"
    url = "/categories/"
    title = ""
  [[menu.main]]
    weight = 5
    identifier = "github"
    pre = "<i class='fab fa-github fa-fw' aria-hidden='true'></i>"
    post = ""
    name = ""
    url = "https://github.com/LucasZhanye"
    title = "GitHub"

# Hugo 解析文档的配置
[markup]
  # 语法高亮设置 (https://gohugo.io/content-management/syntax-highlighting)
  [markup.highlight]
    # false 是必要的设置 (https://github.com/dillonzq/LoveIt/issues/158)
    codeFences = true
    guessSyntax = true
    lineNos = true
    lineNumbersInTable = true
    # false 是必要的设置
    # (https://github.com/dillonzq/LoveIt/issues/158)
    # Goldmark 是 Hugo 0.60 以来的默认 Markdown 解析库
    noClasses = false
  # Goldmark 是 Hugo 0.60 以来的默认 Markdown 解析库
  [markup.goldmark]
    [markup.goldmark.extensions]
      definitionList = true
      footnote = true
      linkify = true
      strikethrough = true
      table = true
      taskList = true
      typographer = true
    [markup.goldmark.renderer]
      # 是否在文档中直接使用 HTML 标签
      unsafe = true
  # 目录设置
  [markup.tableOfContents]
    startLevel = 2
    endLevel = 6
[params]
  # 网站默认主题样式 ["auto", "light", "dark"]
  defaultTheme = "light"
  # 公共 git 仓库路径，仅在 enableGitInfo 设为 true 时有效
  #gitRepo = ""
  # LoveIt 新增 | 0.1.1 哪种哈希函数用来 SRI, 为空时表示不使用 SRI
  # ["sha256", "sha384", "sha512", "md5"]
  fingerprint = "sha256"
  # LoveIt 新增 | 0.2.0 日期格式
  dateFormat = "2006-01-02"
  # 网站标题, 用于 Open Graph 和 Twitter Cards
  title = "LucasZhang个人博客"
  # 网站描述, 用于 RSS, SEO, Open Graph 和 Twitter Cards
  description = ""
  # 网站图片, 用于 Open Graph 和 Twitter Cards
  images = ["首页中间的图片"]

# 页面头部导航栏配置
[params.header]
  # 桌面端导航栏模式 ["fixed", "normal", "auto"]
  desktopMode = "normal"
  # 移动端导航栏模式 ["fixed", "normal", "auto"]
  mobileMode = "normal"
  # LoveIt 新增 | 0.2.0 页面头部导航栏标题配置
  [params.header.title]
    # LOGO 的 URL
    logo = "/images/logo.png"
    # 标题名称
    name = "LucasZhang个人博客"
    # 你可以在名称 (允许 HTML 格式) 之前添加其他信息, 例如图标
    pre = ""
    # 你可以在名称 (允许 HTML 格式) 之后添加其他信息, 例如图标
    post = ""
    # LoveIt 新增 | 0.2.5 是否为标题显示打字机动画
    typeit = false

# 页面底部信息配置
[params.footer]
  enable = true
  # LoveIt 新增 | 0.2.0 自定义内容 (支持 HTML 格式)
  custom = ''
  # LoveIt 新增 | 0.2.0 是否显示 Hugo 和主题信息
  hugo = false
  # LoveIt 新增 | 0.2.0 是否显示版权信息
  copyright = true
  # LoveIt 新增 | 0.2.0 是否显示作者
  author = true
  # 网站创立年份
  since = 2022
  # ICP 备案信息，仅在中国使用 (支持 HTML 格式)
  icp = ''
  # 许可协议信息 (支持 HTML 格式)
  license = '<a rel="license external nofollow noopener noreffer" href="https://creativecommons.org/licenses/by-nc/4.0/" target="_blank">CC BY-NC 4.0</a>'

# LoveIt 新增 | 0.2.0 Section (所有文章) 页面配置
[params.section]
  # section 页面每页显示文章数量
  paginate = 20
  # 日期格式 (月和日)
  dateFormat = "01-02"
  # RSS 文章数目
  rss = 10

# LoveIt 新增 | 0.2.0 List (目录或标签) 页面配置
[params.list]
  # list 页面每页显示文章数量
  paginate = 20
  # 日期格式 (月和日)
  dateFormat = "01-02"
  # RSS 文章数目
  rss = 10

# LoveIt 新增 | 0.2.0 应用图标配置
[params.app]
  # 当添加到 iOS 主屏幕或者 Android 启动器时的标题, 覆盖默认标题
  title = "LucasZhang个人博客"
  # 是否隐藏网站图标资源链接
  noFavicon = false
  # 更现代的 SVG 网站图标, 可替代旧的 .png 和 .ico 文件
  svgFavicon = "/logo.svg"
  # Android 浏览器主题色
  themeColor = "#ffffff"
  # Safari 图标颜色
  iconColor = "#5bbad5"
  # Windows v8-10磁贴颜色
  tileColor = "#da532c"

# LoveIt 新增 | 0.2.0 搜索配置
[params.search]
  enable = true
  # 搜索引擎的类型 ["lunr", "algolia"]
  type = "lunr"
  # 文章内容最长索引长度
  contentLength = 4000
  # 搜索框的占位提示语
  placeholder = ""
  # LoveIt 新增 | 0.2.1 最大结果数目
  maxResultLength = 10
  # LoveIt 新增 | 0.2.3 结果内容片段长度
  snippetLength = 50
  # LoveIt 新增 | 0.2.1 搜索结果中高亮部分的 HTML 标签
  highlightTag = "em"
  # LoveIt 新增 | 0.2.4 是否在搜索索引中使用基于 baseURL 的绝对路径
  absoluteURL = false
  ##注册使用，前往：https://www.algolia.com/
  [params.search.algolia]
    index = ""
    appID = ""
    searchKey = ""

# 主页配置
[params.home]
  # LoveIt 新增 | 0.2.0 RSS 文章数目
  rss = 10
  # 主页个人信息
  [params.home.profile]
    enable = true
    # Gravatar 邮箱，用于优先在主页显示的头像
    gravatarEmail = ""
    # 主页显示头像的 URL
    avatarURL = "/images/avatar.png"
    # LoveIt 更改 | 0.2.7 主页显示的网站标题 (支持 HTML 格式)
    title = ""
    # 主页显示的网站副标题 (允许 HTML 格式)
    subtitle = "欢迎来到我的博客，请多多指教"
    # 是否为副标题显示打字机动画
    typeit = true
    # 是否显示社交账号
    social = false
    # LoveIt 新增 | 0.2.0 免责声明 (支持 HTML 格式)
    disclaimer = ""
  # 主页文章列表
  [params.home.posts]
    enable = true
    # 主页每页显示文章数量
    paginate = 6
    # LoveIt 删除 | 0.2.0 被 params.page 中的 hiddenFromHomePage 替代
    # 当你没有在文章前置参数中设置 "hiddenFromHomePage" 时的默认行为
    defaultHiddenFromHomePage = false

# 作者的社交信息设置
[params.social]
  GitHub = ""
  Linkedin = ""
  Twitter = ""
  Instagram = ""
  Facebook = ""
  Telegram = ""
  Medium = ""
  Gitlab = ""
  Youtubelegacy = ""
  Youtubecustom = ""
  Youtubechannel = ""
  Tumblr = ""
  Quora = ""
  Keybase = ""
  Pinterest = ""
  Reddit = ""
  Codepen = ""
  FreeCodeCamp = ""
  Bitbucket = ""
  Stackoverflow = ""
  Weibo = ""
  Odnoklassniki = ""
  VK = ""
  Flickr = ""
  Xing = ""
  Snapchat = ""
  Soundcloud = ""
  Spotify = ""
  Bandcamp = ""
  Paypal = ""
  Fivehundredpx = ""
  Mix = ""
  Goodreads = ""
  Lastfm = ""
  Foursquare = ""
  Hackernews = ""
  Kickstarter = ""
  Patreon = ""
  Steam = ""
  Twitch = ""
  Strava = ""
  Skype = ""
  Whatsapp = ""
  Zhihu = ""
  Douban = ""
  Angellist = ""
  Slidershare = ""
  Jsfiddle = ""
  Deviantart = ""
  Behance = ""
  Dribbble = ""
  Wordpress = ""
  Vine = ""
  Googlescholar = ""
  Researchgate = ""
  Mastodon = ""
  Thingiverse = ""
  Devto = ""
  Gitea = ""
  XMPP = ""
  Matrix = ""
  Bilibili = ""
  Discord = ""
  DiscordInvite = ""
  Lichess = ""
  ORCID = ""
  Pleroma = ""
  Kaggle = ""
  MediaWiki= ""
  Plume = ""
  HackTheBox = ""
  RootMe= ""
  Phone = ""
  Email = ""
  RSS = true # LoveIt 新增 | 0.2.0

# LoveIt 更改 | 0.2.0 文章页面全局配置
[params.page]
  # LoveIt 新增 | 0.2.0 是否在主页隐藏一篇文章
  hiddenFromHomePage = false
  # LoveIt 新增 | 0.2.0 是否在搜索结果中隐藏一篇文章
  hiddenFromSearch = false
  # LoveIt 新增 | 0.2.0 是否使用 twemoji
  twemoji = false
  # 是否使用 lightgallery
  lightgallery = true
  # LoveIt 新增 | 0.2.0 是否使用 ruby 扩展语法
  ruby = true
  # LoveIt 新增 | 0.2.0 是否使用 fraction 扩展语法
  fraction = true
  # LoveIt 新增 | 0.2.0 是否使用 fontawesome 扩展语法
  fontawesome = true
  # 是否在文章页面显示原始 Markdown 文档链接
  linkToMarkdown = false
  # LoveIt 新增 | 0.2.4 是否在 RSS 中显示全文内容
  rssFullText = false
  # LoveIt 新增 | 0.2.0 目录配置
  [params.page.toc]
    # 是否使用目录
    enable = true
    # LoveIt 新增 | 0.2.9 是否保持使用文章前面的静态目录
    keepStatic = false
    # 是否使侧边目录自动折叠展开
    auto = true
  # LoveIt 新增 | 0.2.0 代码配置
  [params.page.code]
    # 是否显示代码块的复制按钮
    copy = true
    # 默认展开显示的代码行数
    maxShownLines = 50
# LoveIt 更改 | 0.2.0 KaTeX 数学公式
[params.page.math]
  enable = true
  # LoveIt 更改 | 0.2.11 默认行内定界符是 $ ... $ 和 \( ... \)
  inlineLeftDelimiter = ""
  inlineRightDelimiter = ""
  # LoveIt 更改 | 0.2.11 默认块定界符是 $$ ... $$, \[ ... \], \begin{equation} ... \end{equation} 和一些其它的函数
  blockLeftDelimiter = ""
  blockRightDelimiter = ""
  # KaTeX 插件 copy_tex
  copyTex = true
  # KaTeX 插件 mhchem
  mhchem = true
# LoveIt 新增 | 0.2.0 Mapbox GL JS 配置
[params.page.mapbox]
  # Mapbox GL JS 的 access token
  accessToken = ""
  # 浅色主题的地图样式
  lightStyle = "mapbox://styles/mapbox/light-v10?optimize=true"
  # 深色主题的地图样式
  darkStyle = "mapbox://styles/mapbox/dark-v10?optimize=true"
  # 是否添加 NavigationControl
  navigation = true
  # 是否添加 GeolocateControl
  geolocate = true
  # 是否添加 ScaleControl
  scale = true
  # 是否添加 FullscreenControl
  fullscreen = true
# LoveIt 更改 | 0.2.0 文章页面的分享信息设置
[params.page.share]
  enable = true
  Twitter = true
  Facebook = true
  Linkedin = false
  Whatsapp = false
  Pinterest = false
  Tumblr = false
  HackerNews = false
  Reddit = false
  VK = false
  Buffer = false
  Xing = false
  Line = false
  Instapaper = false
  Pocket = false
  Flipboard = false
  Weibo = true
  Blogger = false
  Baidu = true
  Odnoklassniki = false
  Evernote = false
  Skype = false
  Trello = false
  Mix = false
# LoveIt 更改 | 0.2.0 评论系统设置
[params.page.comment]
  enable = false
  # Disqus 评论系统设置
  [params.page.comment.disqus]
    # LoveIt 新增 | 0.1.1
    enable = false
    # Disqus 的 shortname，用来在文章中启用 Disqus 评论系统
    shortname = ""
  # Gitalk 评论系统设置
  [params.page.comment.gitalk]
    # LoveIt 新增 | 0.1.1
    enable = false
    owner = ""
    repo = ""
    clientId = ""
    clientSecret = ""
  # Valine 评论系统设置
  [params.page.comment.valine]
    enable = false
    appId = ""
    appKey = ""
    placeholder = ""
    avatar = "mp"
    meta= ""
    pageSize = 10
    # 为空时自动适配当前主题 i18n 配置
    lang = ""
    visitor = true
    recordIP = true
    highlight = true
    enableQQ = false
    serverURLs = ""
    # LoveIt 新增 | 0.2.6 emoji 数据文件名称, 默认是 "google.yml"
    # ["apple.yml", "google.yml", "facebook.yml", "twitter.yml"]
    # 位于 "themes/LoveIt/assets/lib/valine/emoji/" 目录
    # 可以在你的项目下相同路径存放你自己的数据文件:
    # "assets/lib/valine/emoji/"
    emoji = ""
  # Facebook 评论系统设置
  [params.page.comment.facebook]
    enable = false
    width = "100%"
    numPosts = 10
    appId = ""
    # 为空时自动适配当前主题 i18n 配置
    languageCode = "zh_CN"
# LoveIt 新增 | 0.2.0 Telegram Comments 评论系统设置
[params.page.comment.telegram]
  enable = false
  siteID = ""
  limit = 5
  height = ""
  color = ""
  colorful = true
  dislikes = false
  outlined = false
# LoveIt 新增 | 0.2.0 Commento 评论系统设置
[params.page.comment.commento]
  enable = false
# LoveIt 新增 | 0.2.5 utterances 评论系统设置
[params.page.comment.utterances]
  enable = false
  # owner/repo
  repo = ""
  issueTerm = "pathname"
  label = ""
  lightTheme = "github-light"
  darkTheme = "github-dark"
# giscus comment 评论系统设置 (https://giscus.app/zh-CN)
[params.page.comment.giscus]
  # 你可以参考官方文档来使用下列配置
  enable = false
  repo = ""
  repoId = ""
  category = "Announcements"
  categoryId = ""
  # 为空时自动适配当前主题 i18n 配置
  lang = ""
  mapping = "pathname"
  reactionsEnabled = "1"
  emitMetadata = "0"
  inputPosition = "bottom"
  lazyLoading = false
  lightTheme = "light"
  darkTheme = "dark"
# LoveIt 新增 | 0.2.7 第三方库配置
[params.page.library]
  [params.page.library.css]
    # someCSS = "some.css"
    # 位于 "assets/"
    # 或者
    # someCSS = "https://cdn.example.com/some.css"
  [params.page.library.js]
    # someJavascript = "some.js"
    # 位于 "assets/"
    # 或者
    # someJavascript = "https://cdn.example.com/some.js" 
# LoveIt 更改 | 0.2.10 页面 SEO 配置
[params.page.seo]
  # 图片 URL
  images = []
  # 出版者信息
  [params.page.seo.publisher]
    name = ""
    logoUrl = ""

# LoveIt 新增 | 0.2.5 TypeIt 配置
[params.typeit]
  # 每一步的打字速度 (单位是毫秒)
  speed = 100
  # 光标的闪烁速度 (单位是毫秒)
  cursorSpeed = 1000
  # 光标的字符 (支持 HTML 格式)
  cursorChar = "|"
  # 打字结束之后光标的持续时间 (单位是毫秒, "-1" 代表无限大)
  duration = -1

# 网站验证代码，用于 Google/Bing/Yandex/Pinterest/Baidu
[params.verification]
  google = ""
  bing = ""
  yandex = ""
  pinterest = ""
  baidu = ""

# LoveIt 新增 | 0.2.10 网站 SEO 配置
[params.seo]
  # 图片 URL
  image = ""
  # 缩略图 URL
  thumbnailUrl = ""

# LoveIt 新增 | 0.2.0 网站分析配置
[params.analytics]
  enable = true
  [params.analytics.google]
    id = ""
    # whether to anonymize IP
    # 是否匿名化用户 IP
    anonymizeIP = true
   # Fathom Analytics
   # 百度统计,自己配置的
  [params.analytics.baidu]
    id = ""
#  Cookie 许可配置
#[params.cookieconsent]
  #enable = true
  # 用于 Cookie 许可横幅的文本字符串
  #[params.cookieconsent.content]
    #message = ""
    #dismiss = ""
    #link = ""

#  第三方库文件的 CDN 设置
[params.cdn]
  # CDN 数据文件名称, 默认不启用
  # ["jsdelivr.yml"]
  # 位于 "themes/LoveIt/assets/data/cdn/" 目录
  # 可以在你的项目下相同路径存放你自己的数据文件:
  # "assets/data/cdn/"
  data = ""

#  兼容性设置
[params.compatibility]
  # 是否使用 Polyfill.io 来兼容旧式浏览器
  polyfill = false
  # 是否使用 object-fit-images 来兼容旧式浏览器
  objectFit = false
# 网站地图配置
[sitemap]
  changefreq = "weekly"
  filename = "sitemap.xml"
  priority = 0.5

# Permalinks 配置
[Permalinks]
  # posts = ":year/:month/:filename"
  posts = ":filename"

# 隐私信息配置
[privacy]
  #  Google Analytics 相关隐私 (被 params.analytics.google 替代)
  [privacy.googleAnalytics]
    # ...
  [privacy.twitter]
    enableDNT = true
  [privacy.youtube]
    privacyEnhanced = true

# 用于输出 Markdown 格式文档的设置
[mediaTypes]
  [mediaTypes."text/plain"]
    suffixes = ["md"]

# 用于输出 Markdown 格式文档的设置
[outputFormats.MarkDown]
  mediaType = "text/plain"
  isPlainText = true
  isHTML = false

# 用于 Hugo 输出文档的设置
[outputs]
  home = ["HTML", "RSS", "JSON"]
  page = ["HTML", "MarkDown"]
  section = ["HTML", "RSS"]
  taxonomy = ["HTML", "RSS"]

```

其实也是差不多的，就是改一改配置，只不过我没有怎么折腾其他花里胡哨的，暂时够用即可。
需要注意的一个是这里的`baseURL`,本地调试没啥感觉，这个用于结合Github Page来部署静态站点时指定的URL。暂时先不管呗

### 5. 部署到Github Page
有了前4步，一个静态博客网站其实已经成型了，只不过现在都是在你本地的，别人也没法访问到你的博客网站，这样好像也没啥意义。

因此我们需要借助一个平台来部署我们的博客，这里选择了Github Page，好像Gitee也是可以的，不过没试过，感兴趣可以去试试看哈。

其实最简单粗暴的方法可以很简单：
#### 5.1 创建一个Github仓库
第一步，先到Github中创建一个仓库，仓库名称为`XXX.github.io`（XXX为你Github账号的用户名，替换即可），权限为`public`

#### 5.2 上传
第二步，就是进到你站点的根目录，你会发现有一个`public`目录，这个目录就是你本地运行时生成的（也可以运行hugo命令部署生成），里面就是hugo给你渲染后的静态站点的资源文件这些，进入这个目录，把他上传到刚刚创建的Github仓库中，具体步骤就是：
```shell
git init
git remote add origin 你的Github仓库的路径
git add .
git commit -m "First commit"
git push -u origin master
```
#### 5.3 完结
完成第二步后，去到Github页面确认文件都已经上传了，然后就可以直接打开浏览器，访问`https://xxxx.github.io`。就可以看到你的博客站点了。

#### 5.4 以后
以后你想要发布博客，那你就得经历以下几个步骤哈：
1. 本地生成博客文件(xxx.md)，编辑
2. 本地运行看看效果，记得将draft改为true
3. 执行hugo命令进行编译
4. 进入public目录，执行git命令上传:
	1. `git add .` 
	2. `git commit -m "xxxx"`
	3. `git push`
5. 查看网站

### 6. 小结
这一篇主要简单说下Hugo搭建过程，可能细节部分不是很到位，但是作为一个简单记录足够了，也不是什么重点内容，仅仅是记录一番。

PS：是不是会觉得这个博客文章的发布流程有些繁琐，确实是这样的，那有没有好的方法简化步骤？答案是有的，下一篇[Hugo与Obsidian搭配食用](Hugo与Obsidian搭配食用.md)继续唠嗑。

