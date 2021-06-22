#### Git凭证存储

==其实是HTTP协议下的凭证存储问题==

#### 默认储存在哪里

默认是使用辅助程序，如果没有，每次链接都需要输入用户名密码。

如果你是Mac系统，git默认是提供`osxkeychain`辅助程序来管理你的密码。

```
credential.helper=osxkeychain //Mac中默认辅助程序
```

如果你是Windows系统，可以安装[git-credential-winstore](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)，或者是自带的`GitGUI`,提供辅助程序

```
credential.helper=manager  //win中默认辅助程序
```

**Git中管理辅助程序的命令**

获取密码（get）/设置密码（store）/删除密码（erase）/  卸载 (uninstall )

```
//win下
git credential-manager get  	
git credential-manager store 	
git credential-manager erase	
git credential-manager uninstall 

//Mac下
git credential-osxkeychain get
git credential-osxkeychain store
git credential-osxkeychain erase
git credential-osxkeychain uninstall 
```

#### 内存中缓存短时缓存

 cache 会将凭证存放在内存中一段时间，密码不会被存储在磁盘中，默认15分钟后从内存中清除

```
git config --global credential.helper store
```

#### 永久明文硬盘存储

store 账号，密码明文存储在文件里

`credential.helper`设置为 store 可以指定路径 参数：--file

```
git config --global credential.helper store
```

删掉那些store存储怎么处理？

```
git config --global --unset credential.helper -f
```

#### 参考资料

[Git凭证存储（简单易懂，一学就会，认真看）](https://www.cnblogs.com/volnet/p/git-credentials.html)

