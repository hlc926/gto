# gto
Configurable remote host login script (with scp function)  
可配置的远程主机登录脚本（带scp功能）


### 功能:
登录一些开发机、测试机或生产机的时侯，有些机器不是直接连通的，需要跳转。  
例如，想从**机器ms**登录到**机器mo**，需要**先登录ma，然后mb，然后mc，才能到达mo**。

借助perl-Expect可以实现自动登录，一个命令即可以从ms登录到mo。  
同时考虑到需要登录到不同的目标机器，为了避免增加一个目标都需要修改登录脚本，所以支持配置化。

**gto**(perl脚本)实现**自动登录**功能，登录信息通过文件 ***knownhosts*** 配置，并且支持**双向拷贝文件**。

### 使用:
#### 修改配置文件 (具体参看knownhosts)
1. 增加主机登录信息 ma mb mc mo (每个ip-port-user一行，以 === 开头，包括IP、端口、用户等)。
2. 增加路由信息 (**===== mo ma mb mc mo**， 以 ===== 开头，表示要登录mo，需先登录ma，然后mb，然后mc，才能到达mo)。

第2点的路由信息可以无，这样的话只能登录ma，因为到mb、mc、mo的路不通。  
第2点的路由信息配置好后，不仅可以登录的mo，也可以登录到mb或mc(不需要再单独配置)。

要用scp功能，还需配置一台**transit**用于中转，因为机器可能不能连通。(具体参看knownhosts)

#### 运行 gto 登录 
**gto** 和 **knownhosts**需要在同一目录(这台机器需要perl 及 perl-Expect)。
	
	gto f14				# 登录f14
	gto mo				# 登录mo
	gto mc				# 登录mc
	gto ma				# 登录ma
	gto -scp hd80:/data/test.tar mo:/tmp/	# 将机器hd80的/data/test.tar scp到机器mo的 /tmp/ 目录下(注意最后需要一个/，否则认为是 /目录下的 文件tmp)

直接运行`gto`可得到使用帮助:

	可以通过标帜(ip-port-user or 别名 or 路由)登录.
	usage: gto <lflag>        ## lflag 登录标帜
	usage: gto -showlflag     ## 显示所有可用于登录的标帜
	usage: gto -showhosts     ## 显示所有配置的主机信息
	usage: gto -showalias     ## 显示所有配置的主机别名
	usage: gto -scp hostsrc:/path/filesrc hostobj:/path/[fileobj] #scp. 将机器hostsrc的/path目录下的文件filesrc scp到机器hostobj的/path/[fileobj]

#### 其它
也可以增加路由信息 (**===== test ma **)，这样 `gto test` 等价于`gto ma`。

