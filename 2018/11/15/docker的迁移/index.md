# docker容器的迁移


原来在`vultr`买的`vps`内存有点小，准备留着备用，或者放弃掉。（2.5 美金的机器，现在已经绝种的套餐，放弃掉太可惜了。）

另外准备了一台 1G 内存的`vps`，需要做的事情就是把以前的`数据库`、`git数据`、各种东西迁移过去。所幸全部应用都已经用`docker`实现了容器化，有用数据都放在`volume`里面。迁移起来非常方便。

<!--more-->

## 准备工作

原来的`vps`在`vultr`中，叫`Src`，新的`vps`叫`Dest`。分别建个目录做备份。

```shell
# from Src
:~$mkdir backup


# from dest
:~$mkdir backup
```

## 打包

我的数据都是放在 volume 里面的。首先是在`Src`中用一个`container`把`volume`加载。这个就个人随意了。

然后将`volume`里面的东西打包

```shell
# from Src
sudo docker run -v /gogs --name store alpine /bin/ash
sudo docker run --rm --volumes-from store -v $(pwd):/backup alpine tar czf /backup/gogs.tar.gz /volumes/gogs
```

## 传输

用 scp 将打包文件从`Src`下载到`Dest`中。

```shell
cd ~/backup
scp username@ipaddress:~/backup/gogs.tar.gz gogs.tar.gz

```

## 恢复

同样创建一个`volume`，然后用一个临时的`container`加载。然后解包。

```shell
sudo docker volume create gogs
sudo docker run -v /gogs --name store alpine /bin/ash

sudo docker run --rm --volumes-from store -v $(pwd):/backup alpine ash -c "cd /volumes/gogs && tar xzf /backup/gogs.tar.gz --strip 2"
```

all done.

