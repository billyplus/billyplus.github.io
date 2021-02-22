# 通用翻墙脚本


比如我们在 go get 的时候，会遇到有些包要翻墙才能下载。在 npm install 的时候，node-sass 有些文件要翻墙下载。这些工具都会依赖于环境变量里面的`http_proxy`或者`https_proxy`，需要翻墙的时候，只要设置好这些环境变量，工具就能翻墙了。但是针对每个工具写一个脚本并不是一种可以接受的方案，我们需要更通用的方案。

参考[ZeppLu](https://github.com/Microsoft/WSL/issues/2122#issuecomment-302153759)的方案。

<!--more-->

## Windows 下的方案

在`PATH`目录下新建一个`proxy.bat`的批处理

```bat
@echo off

set http_proxy=socks5://127.0.0.1:1080
set https_proxy=127.0.0.1:1081

%*

echo ...

pause
```

## Linux 下的方案

在`.bashrc`或者`.profile`（取决于你使用的 tty）中增加以下设置

```bash
alias socks='ALL_PROXY=socks5://127.0.0.1:1080/ \
        http_proxy=http://127.0.0.1:1081/ \
        https_proxy=http://127.0.0.1:1081/ \
        HTTP_PROXY=http://127.0.0.1:1081/ \
        HTTPS_PROXY=http://127.0.0.1:1081/'
```

## 使用效果

```bat
>proxy go get -u -v golang.org/x/net
Fetching https://golang.org/x/net?go-get=1
Parsing meta tags from https://golang.org/x/net?go-get=1 (status code 200)
get "golang.org/x/net": found meta tag get.metaImport{Prefix:"golang.org/x/net", VCS:"git", RepoRoot:"https://go.googlesource.com/net"} at https://golang.org/x/net?go-get=1
go: finding golang.org/x/net latest
Fetching https://golang.org/x?go-get=1
Parsing meta tags from https://golang.org/x?go-get=1 (status code 200)
Fetching https://golang.org?go-get=1
Parsing meta tags from https://golang.org?go-get=1 (status code 200)
...
请按任意键继续. . .
```

```bash
 ~ socks go get -u -v golang.org/x/crypto
Fetching https://golang.org/x/crypto?go-get=1
Parsing meta tags from https://golang.org/x/crypto?go-get=1 (status code 200)
get "golang.org/x/crypto": found meta tag get.metaImport{Prefix:"golang.org/x/crypto", VCS:"git", RepoRoot:"https://go.googlesource.com/crypto"} at https://golang.org/x/crypto?go-get=1
golang.org/x/crypto (download)
package golang.org/x/crypto: no Go files in /e/project/go/src/golang.org/x/crypto
```

