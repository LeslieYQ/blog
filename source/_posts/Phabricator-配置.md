title: Phabricator 配置
date: 2015-11-16 14:32:49
tags: Phabricator facebook code review
---

首先说明,主要参考官方文档以及陈晓伟  xiaowei@multicorewareinc.com翻译的中文安装使用指南。里面有我自己试验探索出来的各种问题和解决方案。

Phabricator是一个LAMP应用套件，因此最基本的要求就是LAMP环境，这是基础，所以你能使用Linux、Mac OS 但是无法使用Windows来作为你的服务器。其他依赖项： APache（nginx、lighttpd）、MYSQL、PHP（>5.2）、git、PHP的扩展。

你可以选择自己安装各个依赖项组件，然后配置或者直接使用官方提供的安装脚本，建议初次使用，搭建实验和试用的使用安装脚本，简单方便。最好使用全新的系统，免得干扰。

  RedHat衍生版本：http://www.phabricator.com/rsrc/install/install_rhel-derivs.sh

  Ubuntu：http://www.phabricator.com/rsrc/install/install_ubuntu.sh

如果要自己搭建，请参照官方文档说明：https://secure.phabricator.com/book/phabricator/article/installation_guide/

安装完毕后，配置你的webserver，以后都以安装脚本为例，使用的是Apache。修改 httpd.conf 加入 serverName localhost。 重启Apache： service apache restart 或者 /etc/init.d/apache2 restart.

打开浏览器，在地址栏中输入 你的服务器地址 http://你的服务器ip。看是否会出现“It works”的字样，如果出现代表Apache运行正常。否则，就需要去查看一下httpd.conf是否配置正确了。如果 端口冲突，记得修改端口。

如果有问题，请查看Apache的帮助文档。确保mod_php和mod_rewrite启用，如果你设置SSL，请开启mod_ssl模块。这里得模块都是默认开启的。

如果你还没有设置一个域名指向你将要安装的主机上。你可以安装Phabricator到一个二级域名（如phabricator.example.com）上或一个完整域名上，但你不能安装到一个已经存在的网站的某个子目录下。输入你将要安装到的域名以确保 Apache可以为其正常服务，并且DNS已经正确配置。

注意：域名必须包含点（.），而不只是一个名称，如http://example/。否则，一些web浏览器将无法设置cookies。

现在，可以创建一个VirtualHost条目（放置Phabricator到一个二级域名上）或编辑Directory条目的DocumentRoot。将如下所示：

  		<VirtualHost*>

		# Change this to the domain which points to your host.

		ServerNamephabricator.example.com

		# Change this to the path where you put 'phabricator' when you checked it。

		# out from GitHub when following the Installation Guide.

		# Make sure you include "/webroot" at the end!

		DocumentRoot/path/to/phabricator/webroot

		RewriteEngine on

		RewriteRule^/rsrc/(.*)-                          [L,QSA]

		RewriteRule^(.*)$  /index.php?__path__=$1[B,L,QSA
		RewriteRule^/favicon.ico-                             [L,QSA]


然后重启apache，接着你可以在你的浏览器上看到phabricator页面了。

![截图](http://upload-images.jianshu.io/upload_images/198062-dafb56874ed10503.png)


是不是很开心，感觉胜利在望，只能说你图样，想多了，孩子。路还长着呢。看到上面的提示了么？ 点击issue列表，一个个的点击去解决吧，每个都有提示的。可以按照提示解决，如果英文有问题，参照中文安装手册----http://wenku.baidu.com/view/b2fd127b312b3169a451a44a.html

配置中注册是需要发送邮件的，如果是自己的服务器或者阿里云之类的，有2中选择比较靠谱，

PhabricatorMailImplementationPHPMailerLiteAdapter ： 默认的，只需要给服务器安装 sendmail服务即可，但是有个问题，邮件名是使用默认的，可能会被自己公司反垃圾邮件给屏蔽了，接受不到邮件。

PhabricatorMailImplementationPHPMailerAdapter： 利用SMTP去使用大家广泛使用的邮箱，例如163、QQ、Sina、SoHO等，但是记得在上述邮箱中打开响应设置。

配置完成了，重启PHD，然后你发现，能创建用户了，能登录系统了，能点击各种按钮，现在可以使用其他的功能，但是你创建repository之后，无论是 git hg svn 都各种失败，所以来说说怎么配置这个地方，这个地方，官方文档讲的比较模糊，而且中文文档也只是翻译，很多没说清楚，至少我这么认为的。

首先PHA（表示phabricator，以后就这么缩写了）支持2中方式：SSH 和 HTTP。支持3中代码版本管理：Git、SVN、Mercurial（hg）。

参照官方文档：https://secure.phabricator.com/book/phabricator/article/diffusion_hosting/

里面提到3种用户账号，其中 daemon-user 用来启动守护进程phd，建议直接用root，因为他需要root权限，否则你需要新建一个，并且给此用户root权限。

www-user这个如果是使用安装脚本，apache已经有了 www-data 就是，每个系统可能会有差别，记住这个就是 apache 的启动用户，如果自己配置的，查看配置，使用nginx的自己查看，启动nginx的用户。

最后一个vcs-user 这个是用来启用ssh的，基本需要自己手动创建一个新的用户。

接着配置 /etc/sudoers，这里有几点要注意: 一个是 路劲， vcs用户的git-upload-pack等的路径需要注意，因为PHA有的 environment.path-bin的配置项，里面有path路径，如果你配置的路径不包含在里面，貌似会有问题的。 最好自己多用命名查看下。另外一个是 www用户 git-http路径，文档直接给的 /usr/bin/git-http-backend. 但是我的系统里面没有这个文件，于是我修改到我的 git-core 路径去了，但是各种不起作用，最后我在 /usr/bin 下面建立了一个软连接才成功。

需要将phd.user设置为daemon-user：phabricator/$ ./bin/config set phd.user daemon-user

SSH配置比较麻烦，容易出错，如果不配置就没办法使用 ssh的形式来clone代码。

移动SSHD端口， 必须要配置，我在这里费时很久，我开始以为这个只是多一个守护进程，可以使用默认的22端口，一点也不影响，结果就是ssh各种不成功，提示的错误也是千奇百怪，一直调整各种配置。 这里修改端口的意义，在于区分 普通正常的ssh到服务器和使用PHA的ssh clone代码。 建议用文档的方式，安全一点，其实就是 修改 sshd_config的 Port 属性，然后 service ssh restart。

配置并启动Phabricator SSHD。这个按照文档走就行，比较简单，如果不复制，直接使用原文档，修改下也行的。注意里面的 user 要修改成你自己新建的VCS用户。然后启动 sshd。 这里使用SSHD命令的时候，注意是用绝对路径/usr/sbin/sshd

如果成功，在你的其他机子，添加 ssh 公钥到 PHA系统的 Setting面板，这样你就可以测试了。


		$ ssh -T dweller@secure.phabricator.comphabricator-ssh-exec: Welcome to Phabricator.You are logged in as alincoln.You haven't specified a command to run. This means you're requesting aninteractive shell, but Phabricator does not provide an interactive shell overSSH.Usually, you should run a command like `git clone` or `hg push` rather thanconnecting directly with SSH.Supported commands are: conduit, git-receive-pack, git-upload-pack, hg,svnserve.


哦对了，如果使用 http，要在 setting页面去创建 VCS Password。这个要和登录密码不一样，如果使用 用户名\密码方式认证登录的话。

到这里，整个系统就可以使用，包含代码版本管理，代码审查、bug和问题跟踪、团队合作。

