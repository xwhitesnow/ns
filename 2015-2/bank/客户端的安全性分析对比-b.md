<h1>客户端的安全性分析对比</h1>
<hr />
<h2>- 实验背景</h2>
<ul>
<li>
<p>前提假设：试验主机A在不知情的情况下绕过系统安全监测软件，分别安装了木马程序Keylogger（键盘输入记录软件）和RecordDemon（屏幕截图软件）；</p>
</li>
<li>
<p>实验工具：Keylogger（键盘输入记录软件）和RecordDemon（屏幕截图软件）；</p>
</li>
</ul>
<h2>- 实验步骤</h2>
<ol>
<li>运行Keylogger（键盘输入记录软件），打开XX银行个人登录界面，分别使用键盘输入用户名，密码，及附加码，获得键盘输入截取如下图一；</li>
</ol>
<p>图一：<img src="http://www.mftp.info/20151001/1451333533x-572817430.png" /></p>
<pre><code>结论一：建行没有对键盘记录型软件进行监测，使用键盘输入可以轻易的被恶意程序记录用户的输入字符。
</code></pre>
<p>2.运行RecordDemon（屏幕截图软件），打开XX银行个人登录界面，分别使用键盘输入用户名，并打开软键盘，获得屏幕截图如下图二，退出软键盘后重新打开如下图三；</p>
<p>图二：
<img src="http://www.mftp.info/20151001/1451335693x-572817430.png" /></p>
<p>图三：
<img src="http://www.mftp.info/20151001/1451334752x-572817430.png" /></p>
<p>从图中可以看出，除了数字格键有位置变化之外，其他的字符均没有变化，的其他字符右边都有深色条纹竖线；之后我们分别点击字母y（图四）和数字0（图五）；</p>
<p>图四：
<img src="http://www.mftp.info/20151001/1451336068x-572817430.png" /></p>
<p>图五：
<img src="http://www.mftp.info/20151001/1451336191x-572817430.png" /></p>
<p>对比图二用红色圆圈标记的位置，我们可以直观的看到，y字符后的深色竖条纹宽度变宽，0字符出现了深色竖条纹，我们可以从图中很直观的看出用户分别输入了什么字符。</p>
<p>相关代码：</p>
<pre><code>var style1=&quot;&lt;style&gt;&quot;;

//
//**************************************************************
//  对软键盘上进行样式调整
//  修改日期: (2012-11-6)   
//  @author: wuqinming
//  @version: 1.0
//  @params：
//  @params说明：
//  @condition：无
//**************************************************************
style1+=&quot;.btn_letter { text-align:center; BORDER-RIGHT: 1px solid; BORDER-TOP: 1px none; FONT-SIZE: 18px; BORDER-LEFT: 1px none; CURSOR: pointer; BORDER-BOTTOM: 1px none; width:25px; height:21px;line-height:21px; }&quot;;
style1+=&quot;.btn_num {text-align:center;width:25px;BORDER-RIGHT:1px solid; BORDER-TOP: 1px none; FONT-SIZE: 18px; BORDER-LEFT: 1px none; CURSOR: pointer; BORDER-BOTTOM: 1px none;height:21px;}&quot;;
style1+=&quot;.table_title { height:26px;padding-top: 3px;FILTER: progid:DXImageTransform.Microsoft.Gradient(GradientType=0,StartColorStr=#B2DEF7, EndColorStr=#7AB7DA);}&quot;;
style1+=&quot;.btn_input {BORDER-RIGHT: #2C59AA 1px solid; BORDER-TOP: #2C59AA 1px solid; BORDER-LEFT: #2C59AA 1px solid; CURSOR: pointer; COLOR: black; BORDER-BOTTOM: #2C59AA 1px solid; FONT-SIZE: 12px; FILTER: progid:DXImageTransform.Microsoft.Gradient(GradientType=0, StartColorStr=#ffffff, EndColorStr=#C3DAF5);}&quot;;
style1+=&quot;&lt;/style&gt;&quot;; 
document.write(style1); 
</code></pre>

<p><em>上述所提到的字符右侧深色竖条纹在使用不同的浏览器是会有不同的提现，对用户点击位置后的反馈也相对于不同浏览器有不同的提现，但均会有肉眼可见的反馈形式。相关的对软键盘代码调整放在了抓取了鼠标有效点击之后，建议可以删掉这部分代码。</em></p>
<pre>结论二：建行没有对屏幕记录型软件进行监测，使用软键盘输入也可以轻易的被恶意程序记录用户输入字符时</br>的屏幕状态，虽然不能显示出鼠标相应的点击位置，但是从格键后的深色竖条纹轻易看出用户的输入字符。
</pre>
<pre>通过对比结论一和结论二，我们可以发现，不仅在为主机A安装木马和收取木马获得信息的难易程度上两者相近，</br>在获得木马盗取的用户信息后，获得用户名及密码的难易程度也相近。我们能做的就是提高自我安全意识，避免木马的植入。</pre>
<p><strong><em>建议：取消用户点击位置的反馈</em></strong></p>
<h2>- 网页软键盘构造分析</h2>
<p><strong>点击提示：</strong></p>
<pre><code>div id=&quot;span0&quot; class=&quot;rujian1&quot; title=&quot;软键盘指软件模拟键盘，您可以通过鼠标点击输入字符，防止木马记录键盘输入的密码。&quot; 
onclick=&quot;if (true) { $('LOGPASS').value = ''; password1 = $('LOGPASS'); showkeyboard(); 
$('LOGPASS').readOnly = 1; $('Calc').password.value = ''; };&quot;
</code></pre>

<p><strong><em>可以看出代码实现人员有当前主机没有屏幕截图木马和屏幕录制木马的安全假设。风险存在于任何地方，正是因为有了这样的假设，才会设计给用户回馈点击位置的函数，但是我们要知道这种假设在现在这个屏幕截图和屏幕录制软件如此之多的情况下，是不科学的，尽快更新软键盘的设置是最好的选择。</em></strong></p>
<p><strong>showkeyboard</strong></p>
<pre><code>function showkeyboard()
{
setShowKeyBoard(true);
randomNumberButton();
var th = password1;
var ttop  = th.offsetTop;
var thei  = th.clientHeight;
var tleft = th.offsetLeft;
var ttyp  = th.type;
while (th = th.offsetParent)
{ttop+=th.offsetTop; tleft+=th.offsetLeft;}  
softkeyboard.style.top  = ttop+thei+16+&quot;px&quot;;  
softkeyboard.style.left = tleft-100+&quot;px&quot;;
softkeyboard.style.display=&quot;block&quot;;
password1.readOnly=1;
password1.blur();
$('useKey').focus();
if(null!=hideSelect){
hideSelect();
}
}
&lt;div align=&quot;center&quot; id=&quot;softkeyboard&quot; name=&quot;softkeyboard&quot; style=&quot;position: absolute; left: 14px; top: 141px; width: 400px; z-index: 65535; display: block;&quot;&gt;
&lt;table id=&quot;CalcTable&quot; width=&quot;&quot; border=&quot;0&quot; align=&quot;center&quot; cellpadding=&quot;0&quot; cellspacing=&quot;0&quot; bgcolor=&quot;&quot; style=&quot;border: 1px solid rgb(181, 173, 241);&quot;&gt;
&lt;form id=&quot;Calc&quot; name=&quot;Calc&quot; action=&quot;&quot; method=&quot;post&quot; autocomplete=&quot;off&quot;&gt;&lt;/form&gt;
&lt;tbody&gt;&lt;tr&gt;
&lt;td class=&quot;table_title&quot; title=&quot;尊敬的客户：为了保证网上交易安全,建议使用密码输入器输入密码!&quot; align=&quot;right&quot; valign=&quot;middle&quot; bgcolor=&quot;&quot; style=&quot;cursor: default; background-color: rgb(181, 173, 241);&quot;&gt;
&lt;input type=&quot;hidden&quot; value=&quot;&quot; name=&quot;password&quot;&gt;
&lt;input type=&quot;hidden&quot; value=&quot;ok&quot; name=&quot;action2&quot;&gt;&amp;nbsp;
&lt;font style=&quot;font-weight:bold; font-size:15px; color:#075BC3&quot;&gt;中国建设银行&amp;nbsp;&amp;nbsp;密码输入器&lt;/font&gt;
&amp;nbsp;&amp;nbsp;&amp;nbsp;&amp;nbsp;&amp;nbsp;&amp;nbsp;&amp;nbsp;&amp;nbsp;&amp;nbsp;&amp;nbsp;&amp;nbsp;&amp;nbsp;
&lt;input id=&quot;useKey&quot; class=&quot;btn_letter&quot; style=&quot;width:110px;height:25px;BORDER:1px solid blue; font-size:14px&quot; type=&quot;button&quot; value=&quot;使用键盘输入&quot; bgtype=&quot;1&quot; 
onclick=&quot;password1.readOnly=0;password1.focus();closekeyboard();password1.value='';setShowKeyBoard(false);&quot; 
onkeyup=&quot;if(event.keyCode==13){password1.readOnly=0;password1.focus();closekeyboard();password1.value='';setShowKeyBoard(false);}&quot;&gt;
&lt;span style=&quot;width:2px;&quot;&gt;&lt;/span&gt;&lt;/td&gt;&lt;/tr&gt;
&lt;tr align=&quot;center&quot;&gt;
&lt;td align=&quot;center&quot; bgcolor=&quot;#FFFFFF&quot;&gt;
&lt;table align=&quot;center&quot; width=&quot;%&quot; border=&quot;0&quot; cellspacing=&quot;1&quot; cellpadding=&quot;0&quot;&gt;
&lt;tbody&gt;&lt;tr align=&quot;left&quot; valign=&quot;middle&quot;&gt; 
&lt;tr align=&quot;left&quot; valign=&quot;middle&quot;&gt; 
&lt;td&gt;&lt;input width=&quot;38&quot; height=&quot;38&quot; type=&quot;button&quot; value=&quot;`&quot; class=&quot;btn_letter&quot;&gt;&lt;/td&gt;
&lt;td&gt;&lt;input type=&quot;button&quot; bgtype=&quot;2&quot; name=&quot;button_number1&quot; value=&quot;1&quot; class=&quot;btn_letter&quot;&gt;&lt;/td&gt;
&lt;td&gt; &lt;input type=&quot;button&quot; bgtype=&quot;2&quot; name=&quot;button_number2&quot; value=&quot;8&quot; class=&quot;btn_letter&quot;&gt;&lt;/td&gt;
&lt;td&gt; &lt;input type=&quot;button&quot; bgtype=&quot;2&quot; name=&quot;button_number3&quot; value=&quot;3&quot; class=&quot;btn_letter&quot;&gt;&lt;/td&gt;
&lt;td&gt; &lt;input type=&quot;button&quot; bgtype=&quot;2&quot; name=&quot;button_number4&quot; value=&quot;4&quot; class=&quot;btn_letter&quot;&gt;&lt;/td&gt;
&lt;td&gt; &lt;input type=&quot;button&quot; bgtype=&quot;2&quot; name=&quot;button_number5&quot; value=&quot;9&quot; class=&quot;btn_letter&quot;&gt;&lt;/td&gt;
&lt;td&gt; &lt;input type=&quot;button&quot; bgtype=&quot;2&quot; name=&quot;button_number6&quot; value=&quot;7&quot; class=&quot;btn_letter&quot;&gt;&lt;/td&gt;
&lt;td&gt; &lt;input type=&quot;button&quot; bgtype=&quot;2&quot; name=&quot;button_number7&quot; value=&quot;2&quot; class=&quot;btn_letter&quot;&gt;&lt;/td&gt;
&lt;td&gt; &lt;input type=&quot;button&quot; bgtype=&quot;2&quot; name=&quot;button_number8&quot; value=&quot;6&quot; class=&quot;btn_letter&quot;&gt;&lt;/td&gt;
&lt;td&gt; &lt;input type=&quot;button&quot; bgtype=&quot;2&quot; name=&quot;button_number9&quot; value=&quot;0&quot; class=&quot;btn_letter&quot;&gt;&lt;/td&gt;
&lt;td&gt; &lt;input bgtype=&quot;2&quot; name=&quot;button_number0&quot; type=&quot;button&quot; value=&quot;5&quot; class=&quot;btn_letter&quot;&gt;&lt;/td&gt;
&lt;td&gt; &lt;input type=&quot;button&quot; value=&quot;-&quot; class=&quot;btn_letter&quot;&gt;&lt;/td&gt;
&lt;td&gt; &lt;input type=&quot;button&quot; value=&quot;=&quot; class=&quot;btn_letter&quot;&gt;&lt;/td&gt;
&lt;td&gt; &lt;input type=&quot;button&quot; value=&quot;\&quot; class=&quot;btn_letter&quot;&gt;&lt;/td&gt;
&lt;td&gt; &lt;/td&gt;
&lt;/tr&gt;
&lt;tr align=&quot;left&quot; valign=&quot;middle&quot;&gt; ……&lt;/tr&gt;
&lt;tr align=&quot;left&quot; valign=&quot;middle&quot;&gt; ……&lt;/tr&gt;
&lt;tr align=&quot;left&quot; valign=&quot;middle&quot;&gt; ……&lt;/tr&gt;
&lt;/tbody&gt;&lt;/table&gt;&lt;/td&gt;&lt;/tr&gt;&lt;/tbody&gt;&lt;/table&gt;&lt;/div&gt;
&lt;/code&gt;&lt;/pre&gt;
</code></pre>

<p><strong><em>通过实际测试及阅读代码，软键盘按行依次生成，每次刷新只有数字会随机生成位置，其余字符均保持位置不变。既然已经保持随机生成，选择随机生成全部字符的位置会比只随机生成数字位置会具有更高的安全性。</em></strong></p>
<p><strong>数字随机排列</strong></p>
<pre><code>function randomNumberButton(){
var a = new Array(10);  
a[0]=0;a[1]=1;a[2]=2;a[3]=3;a[4]=4;a[5]=5;a[6]=6;a[7]=7;a[8]=8;a[9]=9;
var randomNum;
var times=10;
for(var i=0;i&lt;10;i++){
randomNum = parseInt(Math.random()*10);
var tmp=a[0];
a[0]=a[randomNum];
a[randomNum]=tmp;
}
Calc.button_number0.value=a[0];
Calc.button_number1.value=a[1];
Calc.button_number2.value=a[2];
Calc.&gt;button_number3.value=a[3];
Calc.button_number4.value=a[4];
Calc.button_number5.value=a[5];
Calc.button_number6.value=a[6];
Calc.button_number7.value=a[7];
Calc.button_number8.value=a[8];
Calc.button_number9.value=a[9];
}
</code></pre>

<p><strong><em>数字随机产生的函数是Math.random()<code>*</code>10,生成0-10的随机数，之后用parseInt取出生成数的整数部分。而math.random也可以随机生成字母，所以也为之前所说的随机生成全部字符提供了基础</em></strong></p>
<h2>- 其他设计分析</h2>
<p><strong>1. 输入次数的限制</strong></p>
<p>根据实际测试，正常情况下每个用户名每天只可以输入错误密码三次，输入错误后此用户名今天将不能实现网页登录。这看似对我们实现暴力攻击，穷举分析增加了难度。我们需要找出整个判断的关键部分。
<img src="http://www.mftp.info/20151001/1451466040x-568361186.png" /></p>
<p>纵观整个前端对密码的判断，我们找到了如下代码:</p>
<pre><code>***对密码长度进行判断，是否超过最大限度
function addValue(newValue)
{
if (CapsLockValue==0)
{
var str=Calc.password.value;
if(str.length&lt;password1.maxLength)
{
Calc.password.value += newValue;
}           
if(str.length&lt;=password1.maxLength)
{
password1.value=Calc.password.value;
pspassword1();
}
}
else
{
var str=Calc.password.value;
if(str.length&lt;password1.maxLength)
{
//Calc.password.value += newValue.toUpperCase();
Calc.password.value += newValue;
}
if(str.length&lt;=password1.maxLength)
{
password1.value=Calc.password.value;
pspassword1();
}
}
}
***提示密码长度限制为&gt;=6位。
function subMitClick(){             
if(!autoCheck()){           
return false;
}
if(inCtl.obj){
if(inCtl.obj.GetLength()&lt;6){        
alert('密码长度必须大于等于6位!');     
inCtl.obj.focus();          
return false;
}
}else{
if(document.jhform.TRANS_PASSWD.value.length&lt;6){
alert('密码长度必须大于等于6位!');
document.jhform.TRANS_PASSWD.focus();
return false;
}
}
var cname=document.jhform.ok.className;
if(cname.indexOf('_dis')!=-1)
    document.jhform.ok.className=cname;
else document.jhform.ok.className=cname+&quot;_dis&quot;;
document.jhform.ok.disabled='true';
return true;
}

***限制用户输入简单密码：
function check_PWDifEasy(strPWD,cardId){
        var strNum;
        var strString;
        var n=0;
        //密码不能设为相同的数字，如000000、111111等 
        if(check_PWDisInt(strPWD) &amp;&amp; strPWD.length==6){
            for(var i=0;i&lt;strPWD.length;i++){
                if(strPWD.charAt(0)==strPWD.charAt(i)){
                    n++;
                }
            }
            if(n==strPWD.length){
                return false;
            }
        }
    //密码不能设为连续升或降排列的数字。如123456、987654等 
    //连续的双重复数字排列：如112233，665544
    //连续的三重复数字排列：如777888，333222
    strNum=&quot;01234567890&quot;;
    if(strPWD.length==6){
        if(strNum.indexOf(strPWD)!=-1) {
             return false;
        }
        strNum=&quot;09876543210&quot;;
        if(strNum.indexOf(strPWD)!=-1){
             return false;
        }
        strNum=&quot;00112233445566778899&quot;;
        if(strNum.indexOf(strPWD)!=-1){
             return false;
        }
        strNum=&quot;99887766554433221100&quot;;
        if(strNum.indexOf(strPWD)!=-1){
             return false;
        }
        strNum=&quot;000111222333444555666777888999&quot;;
        if(strNum.indexOf(strPWD)!=-1){
             return false;
        }
        strNum=&quot;999888777666555444333222111000&quot;;
        if(strNum.indexOf(strPWD)!=-1){
             return false;
        }
    }
    //生日: 如700101(以2位年2位月2位日设置的密码) 如197001(以4位年2位月设置的密码)
    if(cardId.length==15){
        if(strPWD.length==6){
            var temp=cardId.substring(6,12);
            if(strPWD==temp) {
                return false;
            }
            temp = &quot;19&quot; + cardId.substring(6,10);
            if(strPWD==temp){
                return false;
            }
        }
    }
    if(cardId.length==18){
        if(strPWD.length==6){
            var temp = cardId.substring(8,14);
            if(strPWD==temp){
                return false;
            }
            temp = cardId.substring(6,12);
            if(strPWD==temp){
                return false;
            }
        }
    }
    //证件号码中任意连续的6位数字
    if(strPWD.length==6){
        if(cardId.indexOf(strPWD)!=-1){
             return false;
        }
    }
        return true;
}
</code></pre>

<p><strong><em>整个前端对密码的判断都十分简单，只是判断了基础输入的字符长度和是否为简单密码，对输入错误次数的限制没有找到，对于次数的限制，及密码和用户名的判断都以post的形式传递给后端执行。</em></strong></p>
<p><strong>2. 输入错误名字处理</strong></p>
<p>实际测试，输入不存在的用户名会存在如下提示：</p>
<p><img src="http://www.mftp.info/20151001/1451523907x-568361186.png" /></p>
<p><strong><em>可以看到，对于无效的用户名，对测试人提供了甄别的依据，建议可以放弃此种提示，改为对于输入密码或用户名错误提示相同。</em></strong></p>
