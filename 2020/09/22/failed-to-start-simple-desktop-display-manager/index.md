# Failed to start Simple Desktop Display Manager


把`nv`的独显拆下来后，开机进不了`manjaro`，提示`failed to start Simple Desktop Display Manager`。猜测是显示配置不能自动更新。

<!--more-->

查看一下配置

```
ls /etc/X11/xorg.conf.d/ -al

90-mhwd.conf -> /etc/X11/mhwd.d/nvidia.conf
```

显示有个文件：`90-mhwd.conf`，指向的正是 nvidia 的配置，直接把这个文件备份&&删除，重启。

