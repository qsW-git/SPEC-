### RPM包是什么
#### RPM软件包管理器
英文原义：RPM Package Manager（原Red Hat Package Manager，现在是一个递归缩写）
RPM包以.RPM为后缀名，是一种通过数据库记录方式将所需软件安装到主机上的管理程序，可用于互联网上下载包的安装与打包，类似于windows中的setup.exe。
RPM包最大的特点是将要安装的软件先编译并打包，通过包装好的软件中默认的数据库记录，记录这个软件在安装的时候需要的依赖属性模块，在用户的Linux主机安装时，RPM会先根据软件里的记录数据，查询Linux主机的依赖属性软件是否满足，若满足则予以安装，不满足则不安装。安装的时候将该软件的信息全部写入RPM的数据库中以便将来的查询、验证与卸载。

#### RPM安装软件：
在安装RPM类型的文件时，会先去读取文件内记录的设置参数内容，然后将该数据用来对比Linux系统的环境，以找出是否有属性依赖的软件尚未安装的问题。
若环境检查合格，那么RPM文件就被安装到Linux系统上。安装完毕后，该软件的相关信息就被写入到/var/lib/rpm目录下的数据库文件中了。/var/lib/rpm目录十分重要，查询系统以安装软件，升级软件，查询软件详细信息由该数据库进行记录。
![截图](https://github.com/qsW-git/SPEC-/assets/100649126/dc1f69bb-9c33-42f7-b595-66aa758737ca)

RPM包安装错误原因：
> * 非正常关机、误删除运行中的程序文件
> * RPM数据文件被误写或删除

当RPM数据库损坏时，需要进行数据库重建
> * rpm  --rebuilddb 或者 rpm --initdb

#### RPM包用途如下：
1. 安装、删除、升级和管理软件；当然也支持在线安装和升级软件；
2. 通过RPM包管理能知道软件包包含哪些文件，也能知道系统中的某个文件属于哪个软件包；
3. 可以在查询系统中的软件包是否安装以及其版本；
4. 作为开发者可以把自己的程序打包为RPM 包发布；
5. 软件包签名GPG和MD5的导入、验证和签名发布
6. 依赖性的检查，查看是否有软件包由于不兼容而扰乱了系统；

### RPM包的优缺点
1. 优点：
> * 由于已经完成编译并且打包，安装方便；
> * 套件信息被记录于Linux主机数据库中，方便查询，升级卸载；

2. 缺点：
> * 安装环境必须于打包环境一致；
> * 需满足安装软件的依赖需求；
> * 卸载软件时，要从最上层开始，若从底层开始，会出现问题；

### RPM包使用权限
RPM软件的安装、删除、更新只有root权限才能使用；对于查询功能任何用户都可以操作；如果普通用户拥有安装目录的权限，也可以进行安装。RPM包的制作建议使用普通用户，在用户家目录中创建。

### RPM包命名规则
1. 命名格式
>* name-version-arch.rpm
>* name-version-arch.src.rpm

2. 说明
>* name：软件包名称
>*  version：带有主、次和修订的软件包版本。
>* arch：硬件平台。硬件平台包括了：i386、i486、i586、i686、x86_64、ppc、sparc、alpha。
> * src.rpm：源代码包，即该文件提供了源代码，为对提供内容进行编译。
> * 虽然SRMP(*.src.rpm文件)的内容是源代码，但是它仍然含有该软件所需的依赖性软件说明以及所有RPM文件所提供的数据，也提供了参数的配置文件，所以如果我们用的是SRPM的话，安装时，需要先将该软件以RPM管理的方式进行编译，此时SRPM会被编译成RPM文件，然后再将RPM文件安装到Linux系统当中。

3. 例如
>* httpd-2.2.3-29.el5.i386.rpm
>* httpd-devel-2.2.3-29.el5.i386.rpm
>* httpd-manual-2.2.3-29.el5.i386.rpm
>* system-config-httpd-1.3.3.3-1.el5.noarch.rpm

(1)name，如：httpd，是软件的名称。
(2)version，如:2.2.3 ,是软件的版本号。版本号的格式通常为“主版本号.次版本号.修正号”。29，是发布版本号，表示这个RPM包是第几次编译生成的。
(3)arch,如:i386,表示包的适用的硬件平台，目前RPM支持的平台有：i386、i586、i686、sparc和alpha。
(4).rpm或.src.rpm,是RPM包类型的后缀，.rpm是编译好的二进制包，可用rpm命令直接安装；.src.rpm表示是源代码包，需要安装源码包生成源码，并对源码编译生成.rpm格式的RPM包，才可以安装RPM包。

特殊名称：
 1、el* :表示这个软件包的发行商版本,el5表示这个软件包是在RHEL 5.x/CentOS 5.x下使用。
 2、devel：表示这个RPM包是软件的开发包。
 3、noarch：说明这样的软件包可以在任何平台上安装，不需要特定的硬件平台。在任何硬件平台上都可以运行。
 4、manual 手册文档。
 
### RPM包的操作模式
 RPM 有五种基本操作模式（不包括包构建）：安装、卸载、升级、查询和验证。
#### RPM包安装
通常情况下，RPM 包采用系统默认的安装路径，所有安装文件会按照类别分散安装到表 1 所示的目录中。

<font face="黑体" size=2.>表1 示例表格</font></p>
<div class="center">

|   安装路径   |    含 义   |
|  :---:  |  :---:  |
|        /etc        |        配置文件安装目录     |
|      /usr/bin/     |    可执行的命令安装目录     |
|      /usr/lib/     |  程序所使用的函数库保存位置  |
|   /usr/share/doc/  |  基本的软件使用手册保存位置  |
|   /usr/share/man/  |      帮助文件保存位置       |

</div>
RPM 包的默认安装路径是可以通过命令查询的。除此之外，RPM 包也支持手动指定安装路径，但此方式并不推荐。因为一旦手动指定安装路径，所有的安装文件会集中安装到指定位置，且系统中用来查询安装路径的命令也无法使用（需要进行手工配置才能被系统识别），得不偿失。与 RPM 包不同，源码包的安装通常采用手动指定安装路径（习惯安装到 /usr/local/ 中）的方式。既然安装路径不同，同一 apache 程序的源码包和 RPM 包就可以安装到一台 Linux 服务器上（但同一时间只能开启一个，因为它们需要占用同一个 80 端口）

安装RPM包的命令格式为：
```
[root@localhost ~]# rpm -ivh xxx.rpm
```
注意一定是包全名。涉及到包全名的命令，一定要注意路径，可能软件包在光盘中，因此需提前做好设备的挂载工作。
此命令中各选项参数的含义为：
>* -i：安装（install）;
>* -v：显示更详细的信息（verbose）;
>* -h：打印 #，显示安装进度（hash）;
例如，使用此命令安装 apache 软件包，如下所示：
```
[root@localhost ~]# rpm -ivh \
/mnt/cdrom/Packages/httpd-2.2.15-15.el6.centos.1.i686.rpm
Preparing...
####################
[100%]
1:httpd
####################
[100%]
```
注意，直到出现两个 100% 才是真正的安装成功，第一个 100% 仅表示完成了安装准备工作。
此命令还可以一次性安装多个软件包，仅需将包全名用空格分开即可，如下所示：
```
[root@localhost ~]# rpm -ivh a.rpm b.rpm c.rpm
```
如果还有其他安装要求（比如强制安装某软件而不管它是否有依赖性），可以通过以下选项进行调整：
>* -nodeps：不检测依赖性安装。软件安装时会检测依赖性，确定所需的底层软件是否安装，如果没有安装则会报错。如果不管依赖性，想强制安装，则可以使用这个选项。注意，这样不检测依赖性安装的软件基本上是不能使用的，所以不建议这样做。
>* -replacefiles：替换文件安装。如果要安装软件包，但是包中的部分文件已经存在，那么在正常安装时会报"某个文件已经存在"的错误，从而导致软件无法安装。使用这个选项可以忽略这个报错而覆盖安装。
>* -replacepkgs：替换软件包安装。如果软件包已经安装，那么此选项可以把软件包重复安装一遍。
>* -force：强制安装。不管是否已经安装，都重新安装。也就是 -replacefiles 和 -replacepkgs 的综合。
>* -test：测试安装。不会实际安装，只是检测一下依赖性。
>* -prefix：指定安装路径。为安装软件指定安装路径，而不使用默认安装路径。
####RPM包升级
升级RPM包的指令格式为：
```
[root@localhost ~]# rpm -Uvh xxx.rpm
```
-U --upgrade功能如下：
>* 将系统上的现有软件包升级到较新版本；
>* 如果尚未安装旧版本，请安装软件包.
如果升级或者安装成功，显示如下：
```
Preparing...                ########################################### [100%]
   1:tree                   ########################################### [100%]
```
```
####Warning
始终使用-i安装新的内核软件包。对于所有软件包(内核软件包除外)，rpm -uvh指令同时包含安装和升级，建议使用此指令。对于内核软件包，使用rpm -ivh指令，不对旧的内核软件包进行替换，，防止新的内核出现问题，导致系统无法启动。
```
###RPM包的卸载
卸载RPM包的指令格式如下：
```
rpm -e xxx
请注意，该命令只需要包名称，而不需要包文件的全称。如果尝试使用 rpm -e 命令卸载软件包并提供原始的完整文件名，则会收到软件包名称错误。
```
RPM 软件包的卸载要考虑包之间的依赖性，从上层向下层进行卸载。例如，我们先安装的 httpd 软件包，后安装 httpd 的功能模块 mod_ssl 包，那么在卸载时，就必须先卸载 mod_ssl，然后卸载 httpd，否则会报错。如果不考虑依赖性，执行卸载指令会产生依赖性错误，如下：
```
[root@localhost ~]# rpm -e httpd
error: Failed dependencies:
httpd-mmn = 20051115 is needed by (installed) mod_wsgi-3.2-1.el6.i686
httpd-mmn = 20051115 is needed by (installed) php-5.3.3-3.el6_2.8.i686
httpd-mmn = 20051115 is needed by (installed) mod_ssl-1:2.2.15-15.el6.
centos.1.i686
httpd-mmn = 20051115 is needed by (installed) mod_perl-2.0.4-10.el6.i686
httpd = 2.2.15-15.el6.centos.1 is needed by (installed) httpd-manual-2.2.
15-15.el6.centos.1 .noarch
httpd is needed by (installed) webalizer-2.21_02-3.3.el6.i686
httpd is needed by (installed) mod_ssl-1:2.2.15-15.el6.centos.1.i686
httpd=0:2.2.15-15.el6.centos.1 is needed by(installed)mod_ssl-1:2.2.15-15.el6.centos.1.i686
```
####RPM包查询
RPM包查询格式如下：
```
rpm -q[子选项] [软件名]
```
用法：
>* -qa：查看系统中已安装的所有RPM软件包列表；
>* -qi：查看指定软件的详细信息(information)；
>* -ql：列出该软件所有的文件与目录所在的完整文件名(list)；
>* -qc：列出该软件所有的配置文件(找出在/etc下的文件)；
>* -qd：列出该软件所有的说明文件(找出与man相关的文件)；
>* -qR：列出与该软件有关的依赖软件所含的文件(Required)；
查询文件/目录属于那个RPM软件
```
rpm -qf 文件或目录名
```
查询未安装的RPM包
格式：
```
rpm -qp[子选项] [RPM包文件]
```
用法：找出某个尚未安装的RPM包的相关信息；
>* -qpi：通过.rpm包文件查看该软件的详细信息；
>* -qpl：查看.rpm安装包内所包含的目录、文件列表；
>* -qpc：查看.rpm安装包内包含的配置文件列表；
>* -qpd：查看.rpm安装包内包含的文档文件列表；
####RPM包校验
RPM包验证用于判断已安装的软件是否被修改。
用法：
>* -Va : 校验系统中已安装的所有软件包;
>* -V 以安装的包名 : 选项表示校验指定 RPM 包中的文件;
>* -Vf 系统文件名 : 选项表示校验某个系统文件是否被修改。
例如：校验apache 软件包中所有的安装文件是否被修改，可执行如下命令：
```
[root@localhost ~]#vim /etc/httpd/conf/httpd.conf
...省略内容
[root@localhost ~]# rpm -V httpd
S.5....T. c /etc/httpd/conf/httpd.conf(文件被修改的信息)
```
1. 最前面的 8 个字符（S.5....T）都属于验证信息，各字符的具体含义如下：
>* S：文件大小是否改变。
>* M：文件的类型或文件的权限（rwx）是否改变。
>* 5：文件MD5校验和是否改变（可以看成文件内容是否改变）。
>* D：设备的主从代码是否改变。
>* L：文件路径是否改变。
>* U：文件的属主（所有者）是否改变。
>* G：文件的属组是否改变。
>* T：文件的修改时间是否改变。
>* .：若相关项没发生改变，用 . 表示。
2. 被修改文件类型，大致如下：
>*  c：配置文件（configuration file）。
>* d：普通文档（documentation）。
>* g："鬼"文件（ghost file），很少见，就是该文件不应该被这个 RPM 包包含。
>* l：授权文件（license file）。
>* r：描述文件（read me）。
3. 被修改文件的绝对路径(包含文件名）
####RPM包数字证书验证
RPM 包校验方法只能用来校验已安装的 RPM 包及其安装文件，如果 RPM 包本身就被动过手脚，此方法将无法解决问题，需要使用 RPM 数字证书验证方法。
使用数字证书验证 RPM 包的方法具有如下 2 个特点：
1. 必须找到原厂的公钥文件，然后才能进行安装。
2. 安装 RPM 包会提取 RPM 包中的证书信息，然后和本机安装的原厂证书进行验证。如果验证通过，则允许安装；如果验证不通过，则不允许安装并发出警告。
数字证书默认会放到系统中/etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6位置处，通过以下命令也可验证：
```
系统中的数字证书位置
[root@localhost ~]# ll /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
-rw-r--r--.1 root root 1706 6 月 26 17:29 /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
```
数字证书安装命令：
```
[root@localhost ~]# rpm --import /efc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
--import表示导入数字证书
```
数字证书安装完成后，可进行验证：
```
[root@localhost ~]# rpm -qa|grep gpg-pubkey
gpg-pubkey-c105b9de-4e0fd3a3
```
数字证书已成功安装后，在装有数字证书的系统上安装 RPM 包时，系统会自动验证包的数字证书，验证通过则可以安装，反之将无法安装（系统会报错）。
查询数字证书详细信息：
```
[root@localhost ~]# rpm -qi gpg-pubkey-c105b9de-4e0fd3a3
#查询数字证书包的详细信息
Name : gpg-pubkey
Relocations: (not relocatable)
Version : c105b9de Vendor: (none)
Release : 4e0fd3a3 Build Date: 2012年11月12日 星期一 23时05分20秒
Install Date: 2012年11月12日星期一23时05分20秒 Build Host: local host
Group : Public Keys
Source RPM: (none)
Size : 0
License: pubkey
…省略部分输出…
-----END PGP PUBLIC KEY BLOCK----
```
卸载数字证书（不推荐）：
```
[root@localhost ~]# rpm -e gpg-pubkey-c105b9de-4ead3a3
```
