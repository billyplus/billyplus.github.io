# go-get 利用 socks5 代理翻墙下载


Updated: 2019-01-06，新增更通用的脚本方法：[传送门·通用翻墙脚本](https://ybilly.com/2019/01/06/%E9%80%9A%E7%94%A8%E7%BF%BB%E5%A2%99%E8%84%9A%E6%9C%AC/)

<!--more-->

## `git config` 配置代理在`go get`命令中无效

`go get` 下载软件包时遇到的翻墙问题确实很烦人。而且 `git config` 里面配的代理也是会导致`go get`失败的。

因为当你使用 `go get golang.org/x/crypto` 时，需要先访问 `https://golang.org/x/crypto?go-get=1` 来获取版本库的类型是 `git` 或 `svn` 或是其它 。这个阶段 `git config` 配置的代理是不起作用的。所以 `go get`就会失败。

## 直接使用 `ss` 的 `socks5` 代理

在 windows 环境中，网上以前的经验是使用一个软件把 `ss` 的 `socks5` 代理转成 `http` 代理，然后使用 `set http_proxy=http://127.0.0.1:1081` 这样来达到目的。

实际上不需要那么麻烦，经过测试，`go get`命令直接就可以支持 `socks5` 代理。

新建一个批处理 `goget.bat`，放到 `PATH` 环境下。脚本如下：

```batch
@echo off

set http_proxy=socks5://127.0.0.1:1080
set https_proxy=socks5://127.0.0.1:1080

go get -u -v %*

echo ...

pause
```

使用的时候直接打开命令行输入 `goget golang.org/x/crypto` 来看看效果。

```batch
e:\project\go\src>goget golang.org/x/crypto
Fetching https://golang.org/x/crypto?go-get=1
Parsing meta tags from https://golang.org/x/crypto?go-get=1 (status code 200)
get "golang.org/x/crypto": found meta tag get.metaImport{Prefix:"golang.org/x/crypto", VCS:"git", RepoRoot:"https://go.googlesource.com/crypto"} at https://golang.org/x/crypto?go-get=1
golang.org/x/crypto (download)
package golang.org/x/crypto: no Go files in E:\project\go\src\golang.org\x\crypto
...
请按任意键继续. . .
```

这下完美了。

