## RedHat 6.0 X32更换CentOS yum
由于RedHat的yum是需要官网注册购买服务才可用的,所以我需要更换为CentOS的yum软件以及更换为Cent OS的源才可以在RedHat中正常使用yum,不过暂时感觉yum还是远远落后于apt-get的,例如tmux在官方源中就没有.

首先说下基本思路,这个是6.0的版本的例子,如果是其他版本可以参照这个道理是一样的,根据http://mirrors.163.com/centos/6/os/i386/ ，基本可以推断出不同的版本以及不同的CPU的URL,例如如果你是RedHat6.3,基本就是http://mirrors.163.com/centos/6.3/ ，下面罗列的基本就知道个大概只需要根据这个更换下面的url地址就可以了.还有就是RedHat的版本和CentOS的版本应该是一致的，直到当前完成文档的时间，如果你RedHat是6那么建议你选择对应的6的CentOS的版本的软件比较合适。

首先删除原有的yum：

	rpm -aq|grep yum|xargs sudo rpm -e --nodeps
	
下载CentOS的yum：

	cd ~;mkdir soft;cd ~/soft
	wget http://mirrors.163.com/centos/6/os/i386/Packages/yum-3.2.29-40.el6.centos.noarch.rpm
	wget http://mirrors.163.com/centos/6/os/i386/Packages/yum-metadata-parser-1.1.2-16.el6.i686.rpm
	wget http://mirrors.163.com/centos/6/os/i386/Packages/yum-plugin-fastestmirror-1.1.30-14.el6.noarch.rpm

当前soft目录应该只有3个rpm,所以直接执行下面命令：

	sudo rpm -ivh *.rpm
这个时候需要下载源地址:

	cd /etc/yum.repos.d/
	wget http://mirrors.163.com/.help/CentOS6-Base-163.repo
	
这样执行下面的脚本，可以把其中$releasever的替换为6就替换完成了：
	
	sed -i 's/\$releasever/6/g' CentOS6-Base-163.repo

然后把yum的缓存清除，根据当前的源更新缓存即可：

	sudo yum clean all
	sudo yum makecache 

## RedHat5.7更换yum
原因和6一样，但是步骤少许区别，继续更换为CentOS的源，非常感谢163提供这么方便的哈。

主要步骤就是更改repo的源文件为163的，然后import key文件，然后你就可以使用了。

### 更改源
修改rhel源设置文件，路径为：/etc/yum.repos.d/rhel-debuginfo.repo。为以防万一，建议先将此文件做下备份。其他两个文件(redhat.repo，rhel-source.repo)均不需要修改。

	cd /etc/yum.repos.d/    
	cp rhel-debuginfo.repo rhel-debuginfo.repo.bk

修改rhel-debuginfo.repo文件为以下内容，直接粘帖即可：

	[base]
	name=Red Hat Enterprise Linux 5 -Base
	baseurl=http://mirrors.163.com/centos/5/os/$basearch/
	gpgcheck=1
	[update]
	name=Red Hat Enterprise Linux 5 -Updates
	baseurl=http://mirrors.163.com/centos/5/updates/$basearch/
	gpgcheck=1
	[extras]
	name=Red Hat Enterprise Linux 5 -Extras
	baseurl=http://mirrors.163.com/centos/5/extras/$basearch/
	gpgcheck=1
	[addons]
	name=Red Hat Enterprise Linux 5 -Addons
	baseurl=http://mirrors.163.com/centos/5/addons/$basearch/
	gpgcheck=1
上面修改的部分和6的对比可以看到版本不是完全对应的，这个具体可以先到5.7的目录去查看ReadME，已经说明5.7的需要使用5的即可。

### 导入key

	rpm --import http://mirrors.163.com/centos/5/os/x86_64/RPM-GPG-KEY-CentOS-5

然后就可以直接使用了，例如`yum install gcc`，如果可以正常执行就说明成功了。