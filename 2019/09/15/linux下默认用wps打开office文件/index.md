# linux下默认用wps打开office文件


相信很多人都遇到过，Linux 下每次更新 wps 后，都会发生`.docx`、`.xlsx`文件默认打开方式被修改的问题。直接点击打开`office`文件的话，会被认为是打开一个`zip`文件。只有通过右键菜单里面选择`open with wps...`这种方式才能正常使用 wps 打开。

<!--more-->

## 解决办法

在网上搜了一圈，确定是由于`wps`自己加了`mime type`定义的原因。

具体可以`ls /usr/share/mime/packages/`查看一下，该目录下面应该会有几个`wps-office-`开头的文件，

```bash
# 删除多余的mime type
sudo rm /usr/share/mime/packages/wps-office-*
# 更新
sudo update-mime-database /usr/share/mime
```

再单击打开`.xlsx`文件，发现已经可以正常默认使用 wps 打开了。

