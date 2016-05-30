<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="http://music.163.com/outchain/player?type=2&id=41665696&auto=1&height=66"></iframe>

#### 安装Homebrew
　　Homebrew是Mac OSX下一个包依赖管理工具，用它来安装软件非常的方便只需要brew install 软件名这一条命令就可以将你所需要的软件安装好，不用再操心安装过程中软件的依赖问题，这些问题Homebrew统统帮你搞定。Homebrew安装方法也很简单：打开终端，然后输入命令
　　
<!--bash-->
    ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"

#### 安装mongodb
　　如果是之前就已经安装好的Homebrew，那先要更新一下库：
　　
<!--bash-->
	brew update
　　然后执行下面命令安装mongodb：
　　
<!--bash-->
	brew install mongodb
　　--with-openssl参数表示通过ssl源安装，--devel表示安装最新开发版。此时通过mongod --config /usr/local/etc/mongodb.conf命令已经可以启动。（3.0.7版本是如此。）

#### 遇到的几个问题
1.	我第一次安装完以后，装了Robomongo，但是在连接时发现了bug，一直Authorization skip by you的错误。然后改dbpath、logpath等等操作，都不奏效，就把mongodb玩坏了。最后google得到Robomongo还不支持mongodb3，解决方案在此[http://http://liyanjie918.blog.163.com/blog/static/2022729020156261410274/](http://http://liyanjie918.blog.163.com/blog/static/2022729020156261410274/ "使用Robomongo 连接MongoDB 3.x 报 Authorization failed 解决办法")
2.	可能还会遇到找不到/data/db路径，这是mongdb在之前版本时默认的dbpath，如果安装的是2.*的版本可能就会遇到这个问题，就需要:mkdir -p /data/db
另外，最后还会有一个权限问题，用sudo chmod R 当前用户名 /data 命令能够解决。
3.	大致问题如上，出现这些问题主要还是对mac系统文件和权限等不熟，已经太久没有动mongo，所以也有点生疏了。