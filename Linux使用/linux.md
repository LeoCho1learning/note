apt 与 apt-get的区别

## Ubuntu添加或删除PPA仓库

添加PPA源

```shell
sudo add-apt-repository ppa:user/ppa-name
```

删除PPA源

1. 进入源的目录：

   ```shell
   cd /etc/apt/sources.list.d
   ```

2. 找到源所对应的文件删除即可



## 文件与文件夹的权限操作

更改所有者： chown user file/folder

更改用户组所有者：chgrp group file/folder

使用chown可以同时更改所有者与用户组所有者：chown user:group file/folder

## 命名空间

