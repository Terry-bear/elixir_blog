在学习golang的过程中发现，golang一般不会作为新手的第一门语言进行学习，简而言之，golang虽然设计巧妙，但是蕴含了很多的编程技巧在里面，对新手可以说是很不友好。

做过web开发的不管是前端还是后端都会有这样一个疑问，如何对自己的项目依赖包进行管理。前端有npm，后端有gradle。

今天要介绍的就是golang在应对爆发式的第三方开源包时，做出的历史演进。

如果是单纯的写技术文应该是蛮无聊的，所以我会把故事（历史发展）和技术（干货）分开为两个大段来讲。既可以温故知新，又可以让能看到我文章的人觉得，哎，这篇文章还蛮有意思的。

# golang在对包管理的历史演进

**介绍GOPATH和GOROOT**

很多人，包括博主在内，刚开始学习golang的时候都是对这两个东西一头雾水，不知道在说什么，因为博主是前端出生，以个人的理解可以把`GOPATH`看成是`package.json`安装的**全局**第三方包。

**GOROOT**

golang的默认安装路径，会指定版本`GOROOT="/usr/local/Cellar/go/1.11.2/libexec"`

**GOPATH**

- `src`存放源代码(比如：.go .c .h .s等) 按照golang默认约定，go run，go install等命令的当前工作路径（即在此路径下执行上述命令）。
- `pkg`编译时生成的中间文件（比如：.a）　golang编译包时
- `bin`编译后生成的可执行文件（为了方便，可以把此目录加入到 $PATH 变量中，如果有多个gopath，那么使用`${GOPATH//://bin:}/bin`添加所有的bin目录

**没有包管理的情况下**

在golang项目搭建初期，所引用的第三方包是比较少的，可以直接使用`go get` 拉取第三方包，但是相应的就是没有依赖文件，第三方包的引用只能在对应的`import`文件中去寻找，这极大的延长了敏捷开发的时间和成本。

## **vendor**

go作为一个现代化的语言，居然要用这么复杂不直观而又不标准的方法来管理依赖，难怪在早期会有很多人非常不看好go的前景。

为了解决这个问题，go在**1.5**版本引入了`vendor`属性(默认关闭，需要设置go环境变量`GO15VENDOREXPERIMENT=1`)，并在**1.6**版本中默认开启了`vendor`属性。

**`vendor`属性就是让go编译时，优先从项目源码树根目录下的`vendor`目录查找代码(可以理解为切了一次`GOPATH`)，如果`vendor`中有，则不再去`GOPATH`中去查找**

> vendor依旧存在的问题。1. vendor目录中依赖包没有版本信息。这样依赖包脱离了版本管理，对于升级、问题追溯，会有点困难。
2. 如何方便的得到本项目依赖了哪些包，并方便的将其拷贝到vendor目录下？ Manual is fxxk.

**在此基础上的进一步演进**

- godep
- govendor
- glide
- dep

# 细数相关用法

## godep

docker，kubernetes， coreos等go项目很多都是使用godep来管理其依赖，当然原因可能是早期也没的工具可选。

godep早期版本并不依赖`vendor`，所以对go的版本要求很松，go 1.5之前的版本也可以用，只是行为上有所不同。在`vendor`推出以后，godep也改为使用`vendor`了。

godep使用很简单：当你的项目编写好了，使用GOPATH的依赖包测试ok了的时候，执行：

`$ godep save`

以**hcache**为例，执行`go save`，会做2件事：

- 扫描本项目的代码，将`hcache`项目依赖的包及该包的版本号(即`git commit`)记录到`Godeps/Godeps.json`文件中
- 将依赖的代码从`GOPATH/src`中copy到`vendor`目录(忽略原始代码的.git目录)。对于不支持`vendor`的早期版本，则会拷贝到`Godeps/_workspace/`里

一个`Godeps.json`的例子

```json
{
  "ImportPath": "github.com/silenceshell/hcache",
  "GoVersion": "go1.7",
  "GodepVersion": "v79",
  "Deps": [
      {
          "ImportPath": "github.com/tobert/pcstat",
          "Rev": "91a7346e5b462a61e876c0574cb1ba331a6a5ac5"
      },
      {
          "ImportPath": "golang.org/x/sys/unix",
          "Rev": "0b25a408a50076fbbcae6b7ac0ea5fbb0b085e79"
      }
  ]
}
```

如果要增加新的依赖包：

1. Run go get foo/bar
2. Edit your code to import foo/bar.
3. Run godep save (or godep save ./…).

如果要更新依赖包：

1. Run go get -u foo/bar
2. Run godep update foo/bar. (You can use the … wildcard, for example godep update foo/…).

## golide

glide也是在vendor之后出来的。glide的依赖包信息在glide.yaml和glide.lock中，前者记录了所有依赖的包，后者记录了依赖包的版本信息(合成一个多好)。

```bash
glide create  # 创建glide工程，生成glide.yaml
glide install # 生成glide.lock，并拷贝依赖包
work, work, work
glide update  # 更新依赖包信息，更新glide.lock
```

`glide install`会根据`glide.lock`来更新包的信息，如果没有则会走一把`glide update`生成`glide.lock`

目录结构
```bash
    ──$GOPATH/src/myProject (Your project)
      ├─ glide.yaml
      ├─ glide.lock
      ├─ main.go (Your main go code can live here)
      ├─ mySubpackage (You can create your own subpackages, too)
      |    ├─ foo.go
      ├─ vendor
           ├─ github.com
                ├─ Masterminds
                      ├─ ... etc.
```

功能

- `glide tree`可以很直观的看到vendor中的依赖包(以后会被移除掉，感觉没啥用)
- `glide list`可以列出vendor下所有包
- glide支持的Version Control Systems更多，除了支持git，还支持 SVN, Mercurial (Hg), Bzr
- 最重要的，`glide.yaml`可以指定更多信息，例如依赖包的tag、repo、本package的os, arch。允许指定repo可以解决package名不变。

## govendor

govendor是在vendor之后出来的，功能相对godep多一点，不过就核心问题的解决来说基本是一样的。govendor生成vendor目录的时候需要2条命令：

- `govendor init`生成`vendor/vendor.json`，此时文件中只有本项目的信息
- `govendor add +external`更新`vendor/vendor.json`，并拷贝GOPATH下的代码到vendor目录中。

govendor还可以直接指定依赖包版本来获取包，这也有了点版本管理的影子了。

```bash
    # Setup your project.
    cd "my project in GOPATH"
    govendor init

    # Add existing GOPATH files to vendor.
    govendor add +external

    # View your work.
    govendor list

    # Look at what is using a package
    govendor list -v fmt

    # Specify a specific version or revision to fetch
    govendor fetch golang.org/x/net/context@a4bbce9fcae005b22ae5443f6af064d80a6f5a55
    govendor fetch golang.org/x/net/context@v1   # Get latest v1.*.* tag or branch.
    govendor fetch golang.org/x/net/context@=v1  # Get the tag or branch named "v1".
```

## dep(官网提供)

go发布的dep目前版本处于0.5，下列是博主的dep version信息

```bash
     version     : v0.5.0
     build date  : 2018-08-16
     git hash    : 224a564
     go version  : go1.10.3
     go compiler : gc
     platform    : darwin/amd64
     features    : ImportDuringSolve=false
```

go官网给出了更为全面精准的分析，[**点我跳转**](https://github.com/golang/go/wiki/PackageManagementTools)

就这样咯。👌
