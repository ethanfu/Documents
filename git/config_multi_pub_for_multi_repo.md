如果使用git作为版本控制，那么相信每个人都有一个github的帐号来存储一些自己的项目，公司内部会有自己的远程库，这样的话如果只使用一个默认的pub key的话就会出现冲突没法同时使用。不过这个支持起来不费劲。

## 详解

只需要做以下几个步骤就可以了，根据下面操作：

	ssh-keygen -t rsa -f github -C "xx@gmail.com"
	ssh-keygen -t rsa -f gitlab -C "xx@gmail.com"
,后密码可以为空即可。这样就会在用户的.ssh目录下生成两个pub key和2个private key。如下：

	gitlab gitlab.pub github github.key
这样配置好了以后在`~/.ssh`目录下面执行先`touch config`,然后写入以下内容即可：
	
	host github.com
	    HostName github.com
	    User git
	    IdentityFile ~/.ssh/github
	host git.itari.com.cn
	    HostName git.itari.com.cn
	    User git
	    IdentityFile ~/.ssh/gitlab
	  
然后保存，大功告成。

## 最后

如果存在两个远程的时候，还有一个问题就是git默认是配置了全局的user.email和user.name，这样如果两个远程你用了完全不同的用户名和邮箱就会出现问题，暂时的解决思路应该是这样：如果你公司的用的特别频繁，就配置全局的为公司的帐号和用户名，那么github的项目在项目中单独配置user.email和user.name。如果有其他方式欢迎补充。
