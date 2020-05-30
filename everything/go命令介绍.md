# Command Go

[toc]

Go 是一个管理Go源代码的工具

使用:

```doc
go <command> [arguments]
```

可用的command如下:

```doc
bug         开始一个Bug提交
build       编译包和依赖
clean       移除对象文件和缓存文件
doc         展示包或符号的文档
env         打印Go环境变量
fix         更新包以使用新的 APIs
fmt         使用gofmt重新格式化代码
generate    通过处理源文件生成Go文件
get         添加当前模块的依赖并安装
install     编译并安装包和依赖
list        列出包和模块
mod         模块维护
run         编译和运行Go程序
test        测试包
tool        运行指定的go tool
version     打印Go版本
vet         报告包中可能出现的错误
```

使用`go help <command>`来获得一个命令的更多信息

额外的帮助主题:

```doc
buildmode   build modes
c           calling between Go and C
cache       build and test caching
environment environment variables
filetype    file types
go.mod      the go.mod file
gopath      GOPATH environment variable
gopath-get  legacy GOPATH go get
goproxy     module proxy protocol
importpath  import path syntax
modules     modules, module versions, and more
module-get  module-aware go get
module-auth module authentication using go.sum
module-private module configuration for non-public modules
packages    package lists and patterns
testflag    testing flags
testfunc    testing functions
```

使用`go help <topic>`来获得更多关于主题的信息

## Start a bug report

使用:

```doc
go bug
```

打开默认浏览器并进入github上go项目的issue提交页面, 会自动填写系统信息和Go环境变量

## Compile packages and dependencies

使用:

```doc
go build [-o output] [-i] [build flags] [packages]
```

Build编译由导入路径命名的包及其依赖项，但不安装编译结果。

如果要构建的参数是来自单个目录的.go文件的列表，则build将它们视为指定单个包的源文件列表。

编译包时，build会忽略以“_test.go”结尾的文件。

当编译单个主程序包时，build将生成的可执行文件写入以第一个源文件（“go build ed.go rx.go”写入“ed”或“ ed.exe”）命名的输出文件或源代码目录（'go build unix/sam'写入'sam'或'sam.exe'）。编写Windows可执行文件时，将添加“ .exe”后缀。

当编译多个包或单个非主包时，build编译包，但丢弃结果对象，仅作为可以构建包的检查。

-o标志强制构建以将生成的可执行文件或对象写入命名的输出文件或目录，而不是最后两段中描述的默认行为。如果命名输出是存在的目录，那么所有生成的可执行文件都将写入该目录。

-i标志安装的包是目标的依赖项。

构建标志由构建、清理、获取、安装、列表、运行和测试命令共享:

```doc
-a
    强制重新编译已经是最新的包
-n
    打印编译将运行的命令, 但不会实际运行
-p n
    可以并行运行的进程数量，如构建命令或测试二进制文件(并发编译)。默认值是可用的cpu数量。
-race
    启用数据竞争检测。只支持linux/amd64、freebsd/amd64、darwin/amd64、windows/amd64、linux/ppc64le和linux/arm64(仅支持48位VMA)。
-msan
    启用与内存杀毒器的互操作。只支持linux/amd64、linux/arm64，主机C编译器只支持Clang/LLVM。在linux/arm64上，将使用pie构建模式。
-v
    在编译包时打印它们的名称。
-work
    打印临时工作目录的名称，退出时不要删除它。
-x
    打印编译时运行的命令。

-asmflags '[pattern=]arg list'
    传递给每个go tool asm调用的参数。
-buildmode mode
    要使用的构建模式. 使用 'go help buildmode' 获得更多信息.
-compiler name
    name of compiler to use, as in runtime.Compiler (gccgo or gc).
-gccgoflags '[pattern=]arg list'
    arguments to pass on each gccgo compiler/linker invocation.
-gcflags '[pattern=]arg list'
    arguments to pass on each go tool compile invocation.
-installsuffix suffix
    a suffix to use in the name of the package installation directory,
    in order to keep output separate from default builds.
    If using the -race flag, the install suffix is automatically set to race
    or, if set explicitly, has _race appended to it. Likewise for the -msan
    flag. Using a -buildmode option that requires non-default compile flags
    has a similar effect.
-ldflags '[pattern=]arg list'
    arguments to pass on each go tool link invocation.
-linkshared
    build code that will be linked against shared libraries previously
    created with -buildmode=shared.
-mod mode
     要使用的模块下载模式: readonly, vendor, or mod.
    使用 'go help modules' 获得更多信息.
-modcacherw
    leave newly-created directories in the module cache read-write
    instead of making them read-only.
-modfile file
    in module aware mode, read (and possibly write) an alternate go.mod
    file instead of the one in the module root directory. A file named
    "go.mod" must still be present in order to determine the module root
    directory, but it is not accessed. When -modfile is specified, an
    alternate go.sum file is also used: its path is derived from the
    -modfile flag by trimming the ".mod" extension and appending ".sum".
-pkgdir dir
    install and load all packages from dir instead of the usual locations.
    For example, when building with a non-standard configuration,
    use -pkgdir to keep generated packages in a separate location.
-tags tag,list
    a comma-separated list of build tags to consider satisfied during the
    build. For more information about build tags, see the description of
    build constraints in the documentation for the go/build package.
    (Earlier versions of Go used a space-separated list, and that form
    is deprecated but still recognized.)
-trimpath
    remove all file system paths from the resulting executable.
    Instead of absolute file system paths, the recorded file names
    will begin with either "go" (for the standard library),
    or a module path@version (when using modules),
    or a plain import path (when using GOPATH).
-toolexec 'cmd args'
    a program to use to invoke toolchain programs like vet and asm.
    For example, instead of running asm, the go command will run
    'cmd args /path/to/asm <arguments for asm>'.
```

## Remove object files and cached files 

使用:

```doc
go clean [clean flags] [build flags] [packages]
```

Clean从包源目录中删除对象文件。go命令在一个临时目录中构建大多数对象，所以go clean主要关注其他工具或手动调用go构建留下的对象文件。

如果给出了包参数或设置了-i或-r标志，clean将从与导入路径对应的每个源目录中删除以下文件:

```doc
_obj/            old object directory, left from Makefiles
_test/           old test directory, left from Makefiles
_testmain.go     old gotest file, left from Makefiles
test.out         old test log, left from Makefiles
build.out        old test log, left from Makefiles
*.[568ao]        object files, left from Makefiles

DIR(.exe)        from go build
DIR.test(.exe)   from go test -c
MAINFILE(.exe)   from go build MAINFILE.go
*.so             from SWIG
```

在列表中，DIR表示目录的最后一个path元素，而MAINFILE是构建包时未包含的目录中任何Go源文件的基名。

* -i标志导致clean删除相应的已安装存档或二进制文件(“go install”将创建的文件)。
* -n标志导致clean输出它将执行的删除命令，但不运行它们。
* -r标志导致clean被递归地应用到由导入路径命名的包的所有依赖项。
* -x标志导致clean在执行删除命令时输出这些命令。
* -cache标志导致clean删除整个go构建缓存。
* -testcache标志导致clean令go构建缓存中的所有测试结果过期。
* -modcache标志导致clean删除整个模块下载缓存，包括版本依赖关系的未打包源代码。

使用`'go help build`获得更多关于编译标志的信息
使用`go help packages`获得更多关于指定包的信息

## Show documentation for package or symbol

使用:

```base
go doc [-u] [-c] [package|[package.]symbol[.methodOrField]]
```

Doc打印与由其参数（包，const，func，type，var，method或struct字段）标识的项目相关的文档注释，然后对每个“注释”下的第一级项目进行一行总结。项目（包的包级别声明，类型的方法等）。

Doc接受零个、一个或两个参数。

假设没有参数，也就是说，当这样运行时: 

```doc
go doc
```

它打印在当前目录中package的package文档。如果package是命令（package main），则除非提供了-cmd标志，否则软件包的导出符号将从显示中删除。

当使用一个参数运行时，该参数被视为要记录的项目的类似于Go语法的表示形式。自变量选择的内容取决于GOROOT和GOPATH中安装的内容以及自变量的形式，其形式如下：

```doc
go doc <pkg>
go doc <sym>[.<methodOrField>]
go doc [<pkg>.]<sym>[.<methodOrField>]
go doc [<pkg>.][<sym>.]<methodOrField>
```

与参数匹配的列表中的第一项的文档将会被打印。 （请参见下面的示例。）但是，如果参数以大写字母开头，则假定在当前目录中寻找标识符号或方法。

对于包装，扫描顺序以广度优先顺序按词法确定。也就是说，所展示的package是与搜索匹配的package中最接近根并且在其层次结构上在词法上排在最前面的package。始终在GOPATH之前完整扫描GOROOT树。

如果没有指定或匹配的package，则选择当前目录中的package，因此“ go doc Foo”显示当前package中符号Foo的文档。

件包路径必须是合格路径或路径的正确后缀。 go工具的常用打包机制不适用于：package路径元素，例如.和...没有被go doc实现。

当使用两个参数运行时，第一个必须是完整的程序包路径（不仅是后缀），第二个必须是符号或带有方法或结构字段的符号。这类似于godoc接受的语法：

```doc
go doc <pkg> <sym>[.<methodOrField>]
```

在所有形式中，当匹配符号时，参数中的小写字母可匹配大小写，但大写字母完全匹配。这意味着，如果包中不同的符号具有不同的大小写，则对于小写参数可能存在多个匹配项。如果发生这种情况，将打印所有匹配项的文档。

Examples:

```doc
go doc
    Show documentation for current package.
go doc Foo
    Show documentation for Foo in the current package.
    (Foo starts with a capital letter so it cannot match
    a package path.)
go doc encoding/json
    Show documentation for the encoding/json package.
go doc json
    Shorthand for encoding/json.
go doc json.Number (or go doc json.number)
    Show documentation and method summary for json.Number.
go doc json.Number.Int64 (or go doc json.number.int64)
    Show documentation for json.Number's Int64 method.
go doc cmd/doc
    Show package docs for the doc command.
go doc -cmd cmd/doc
    Show package docs and exported symbols within the doc command.
go doc template.new
    Show documentation for html/template's New function.
    (html/template is lexically before text/template)
go doc text/template.new # One argument
    Show documentation for text/template's New function.
go doc text/template new # Two arguments
    Show documentation for text/template's New function.

At least in the current tree, these invocations all print the
documentation for json.Decoder's Decode method:

go doc json.Decoder.Decode
go doc json.decoder.decode
go doc json.decode
cd go/src/encoding/json; go doc decode
```

可用标记:

```doc
-all
    显示当前package的所有文档。
-c
    匹配符号时要尊重大小写。
-cmd
    将命令(package main)当作常规包对待。否则，在显示package的顶级文档时，会隐藏package main导出的符号。
-short
    每个符号用一行表示。
-src
    显示该符号的完整源代码。这将显示其声明的完整Go源码及定义，例如函数定义（包括正文），类型声明或包含const块。因此，输出可能包括未导出的细节。
-u
    显示未导出的文档以及导出的符号、方法和字段。
```

译自: [Command go](https://golang.org/cmd/go/)