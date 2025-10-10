# 在Ubuntu下安装使用clash

# 1. 压缩包分两种情况

- 方式一  -- `.tar.gz`格式
- 方式二 -- `.gz`格式 ------>比较复杂,但是通用

> 仅对我个人遇到的情况进行分析记录,但是我个人认为第二种方式是通用的
>
> 备注 : 我是在win10系统下的虚拟机的Ubuntu下进行安装使用的,目的是交叉编译时需要用到github下载安装包,没有clash很难交叉编译成功,花了一天半时间研究出了clash的安装和使用,希望这篇博文也能帮到你们,减少你们的开发时间
>
> 接下来就这两种格式进行说明,

## 1.1  方式一(.tar.gz)--有操作界面

1. 创建一个目录并进入目录(目录位置随便)

~~~shell
mkdir clash && cd clash
~~~

2. 下载clash压缩包

~~~shell
wget https://d2141svx4mhk4r.cloudfront.net/Clash.for.Linux-0.20.39-x64.tar.gz
~~~

3. 解压

~~~shell
tar -xvf Clash.for.Linux-0.20.39-x64.tar.gz

//解压之后会得到一个奇怪的目录文件名-->>>'Clash for Windows-0.20.39-x64-linux'
最好是进行重命名:  /* 这里重命名的名称不重要 */
	mv 'Clash for Windows-0.20.39-x64-linux' clash
	
~~~

4. 运行clash

~~~shell
cd clash && ./cfw
/* 这里的clash对应的是'Clash for Windows-0.20.39-x64-linux'改名之后的文件名 */
~~~

- 情况说明

~~~c
/* 
 * 首先,对于这个压缩包解压出来的程序是有UI控制界面的
 * 但是此时clash还没有加载节点,加载节点的方法同win系统加载节点相同
 * 但是Linux相比win系统没有"System Proxy"启动代理这个选项
 * 此时尝试登陆github会发现访问错误
 * 解决方法:
 * 		退出"cfw"程序,重新运行"cfw"程序即可在浏览器正常访问github\google了
 * 注意不要退出终端和UI界面
 */
#在终端结束cfw程序-->     ctrl+c
#重新运行cfw文件---->     ./cfw 
	
不习惯开多个终端的可以使用&进行后台执行:
./cfw &
~~~

## 1.2 方式2(.gz)--没有操作界面

- 首先需要对Ubuntu的网络进行配置
  - 进入系统设置setting-->Network-->Network Proxy
  - 点击设置按钮,之后选中"Manual"--(手动配置),然后进行IP设置和端口设置
  - HTTP   : 127.0.0.1  |  7890
  - HTTPS : 127.0.0.1  |  7890
  - Socks :  127.0.0.1  |  7891
  - 其他的不需要更改

1. 下载clash包

~~~bash
1.1 进入clash官网
	https://www.clash.la/releases/
1.2 找到<clash-linux-amd64-v3>包进行下载,amd代表win架构,会得到<clash-linux-amd64-v3-v1.18.0.gz>

1.3 如果是在win系统下载的,就上传到虚拟机的Ubuntu上
~~~

2. 解压

- 此方法解压得到的已经是一个[应用程序]了,但没有执行权限
- 为了方便, 对解压后的文件进行重命名 
- 并对文件赋予可执行权限

~~~shell
gunzip clash-linux-amd64-v3-v1.18.0.gz

mv clash-linux-amd64-v3-v1.18.0 clash && chmod +x clash
~~~

3. 运行程序

~~~c
./clash
~~~

- 说明: 该方法得到的应用程序没有UI操作界面,所以加载节点方法有些复杂

- 运行程序后, 会在"家目录"下生成一个目录,里面记录这个默认节点的配置文件

  - 需要注意用户模式,普通用户和超级用户的家目录是不一样的

  - 但是只需要执行以下指令就能够查看到配置文件

  - 但会发现,这么默认节点,并没有什么用

    ~~~shell
    cd ~/.config/clash && ls
    vi config.yaml
    ~~~

4. 添加节点

   1. 去自己购买节点的网站----------------------------->复制订阅链接(通用)

   2. 用Linux下浏览器浏览链接,注意链接后要加&flag=clash-----> [链接]&flag=clash

   3. 浏览链接后会自动下载一个文件,这个文件里有我们需要的节点信息

   4. 执行以下指令,修改文件名,并替换之前的默认配置文件

      ~~~shell
      mv <自动下载的文件名,通常是.html结尾> config.yaml
      mv config.yaml ~/.config/clash/
      ~~~

5. 测试节点

- 进入clash程序目录重新启动clash程序

  ~~~shell
  ./clash
  ~~~

- 通过浏览器浏览github或者google

- 也可以在终端找一个github下载链接下载进行测试

