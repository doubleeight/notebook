<div id="cnblogs_post_body" class="blogpost-body"><h1>&nbsp;</h1>
<h1>一. 修改桌面程序图标</h1>
<p>linux的桌面图标都是在/usr/share/applications 目录下的那些 *.desktop文件,修改桌面程序图标就是修改.desktop图标配置文件中Icon的值,这个值对应<strong>&nbsp;/usr/share/icons/hicolor/@x@/apps 目录中的文件名. <span style="background-color: #ffffff; color: #ff0000;">注意: 每个文件夹中文件名必要一致!&nbsp;&nbsp;</span></strong>其中,@x@ 是不同分辨率像素大小: 16x16&nbsp; &nbsp;24x24&nbsp; &nbsp;32x32&nbsp; &nbsp;48x48&nbsp; &nbsp;256x256&nbsp; &nbsp;当然像素大小不只有这几个。</p>
<p>打开任意.desktop修改icon</p>
<p><img src="https://images2018.cnblogs.com/blog/540840/201804/540840-20180404155920665-1600085968.png" alt=""></p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">[Desktop Entry]
Version</span>=<span style="color: #800080;">1.0</span><span style="color: #000000;">
Type</span>=<span style="color: #000000;">Application
Terminal</span>=<span style="color: #0000ff;">false</span><span style="color: #000000;">
Icon[zh_CN]</span>=<span style="color: #000000;">apps.com.qq.im
Exec</span>=/opt/QQ8.<span style="color: #800080;">9.2</span>/qq.<span style="color: #0000ff;">sh</span><span style="color: #000000;">
Name[zh_CN]</span>=Q-<span style="color: #000000;">Q
Name</span>=Q-<span style="color: #000000;">Q
Icon</span>=apps.com.qq.im</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>修改为:</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>#!/usr/bin/<span style="color: #0000ff;">env</span> xdg-<span style="color: #000000;">open
[Desktop Entry]
Version</span>=<span style="color: #800080;">1.0</span><span style="color: #000000;">
Type</span>=<span style="color: #000000;">Application
Terminal</span>=<span style="color: #0000ff;">false</span><span style="color: #000000;">

Exec</span>=/opt/QQ8.<span style="color: #800080;">9.2</span>/qq.<span style="color: #0000ff;">sh</span><span style="color: #000000;">
Name[zh_CN]</span>=Q-<span style="color: #000000;">Q
Name</span>=Q-<span style="color: #000000;">Q
Icon</span>=if_twitter</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p><img src="https://images2018.cnblogs.com/blog/540840/201804/540840-20180404154651387-1101817262.png" alt="" width="357" height="46"></p>
<p>不同的文件夹中每个文件名称都一样!</p>
<p><img src="https://images2018.cnblogs.com/blog/540840/201804/540840-20180404155544577-1365391716.png" alt="" width="197" height="83"></p>
<p>删除/usr/share/icons 目录下的icon-theme.cache文件</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">sudo</span> <span style="color: #0000ff;">rm</span> icon-theme.cache</pre>
</div>
<p>效果:</p>
<p><img src="https://images2018.cnblogs.com/blog/540840/201804/540840-20180404155822014-272531986.png" alt=""></p>
<p><strong>同理,修改默认的linux桌面图标也是一样</strong></p>
<p>&nbsp;我这里用的是ubuntu-mate, 所以打开新建的.desktop文件的icon=mate-panel-launcher,通过搜索文件,可以看到在4个目录里面，只要修改对应的16x16,&nbsp; 22x22,&nbsp; (<strong>24x24)</strong>,&nbsp; 32x32&nbsp; 四目录里面的png文件即可.</p>
<p>&nbsp; &nbsp;</p>
<h1>二.回收站无法清空</h1>
<p>在用户的回收站里删除文件or清空回收站提示:无法删除</p>
<p><img src="https://images2018.cnblogs.com/blog/540840/201804/540840-20180404163334853-1847047387.png" alt="" width="199" height="135"></p>
<p>这是因为我们以root的身份在资源管理器删除文件后,用户回收站里面是么有权限操作的.</p>
<p>解决办法:<strong>/home/USERNAME/.local/share/Trash/files </strong>里面执行命令</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">sudo</span> <span style="color: #0000ff;">rm</span> -rf *</pre>
</div>
<p>&nbsp;</p>
<h1>三.Ubuntu-mate设置面板</h1>
<p>Ubuntu中没有类似Windows的任务栏，但mate中有类似任务栏的栏目叫面板<strong>&nbsp;mate-panel</strong></p>
<p>在面板栏目选择新增到面板</p>
<p>例如： <strong>完整指示器小程序</strong> 添加后如下</p>
<p><img src="https://images2018.cnblogs.com/blog/540840/201806/540840-20180614193016056-1296527556.png" alt=""></p>
<p>例如： <strong>窗口列表</strong>&nbsp; 添加后如下</p>
<h1>&nbsp;<img src="https://images2018.cnblogs.com/blog/540840/201806/540840-20180614193132394-1300398253.png" alt=""></h1>
<p>例如：&nbsp;&nbsp;<strong>通知区域小程序&nbsp;&nbsp;</strong></p>
<p>通知区域 小程序显示一个应用程序活动图标。例如，当您使用 CD 播放器 播放一个 CD 时，一个 CD 光盘图标显示在 通知区域。上面的图就是表示 CD 图标在 通知区域 时的样子。</p>
<p>添加后如下</p>
<p><img src="https://images2018.cnblogs.com/blog/540840/201806/540840-20180615141648550-618188423.png" alt=""></p>
<p>&nbsp;</p>
<h1><a name="autostart"></a>四.设置自启动程序</h1>
<p>在&nbsp;/etc/xdg/autostart/ 目录desktop文件为延迟自启动文件,但不是每个文件都是自启动的,要看desktop文件中是否开启自启动</p>
<p>该自启动为系统级启动</p>
<div class="cnblogs_code">
<pre>AutostartCondition=GSettings org.gwibber.preferences autostart</pre>
</div>
<div class="cnblogs_code">
<pre><span style="color: #000000;"># 将输入法设置自启动的
fcitx</span>-ui-sogou-qimpanel.desktop -&gt; /usr/share/applications/fcitx-ui-sogou-qimpanel.desktop</pre>
</div>
<p>用户级启动目录在:&nbsp;</p>
<div class="cnblogs_code">
<pre>~/.config/autostart</pre>
</div>
<p>同样的取决于desktop文件是否开启自启动</p>
<div class="cnblogs_code">
<pre>X-GNOME-Autostart-enabled=<span style="color: #0000ff;">true</span><span style="color: #000000;">
X</span>-MATE-Autostart-enabled=<span style="color: #0000ff;">true</span></pre>
</div>
<p>&nbsp;</p>
<p>&nbsp;</p>
<h1>五.删除主菜单中不用的菜单链接</h1>
<p>"正规软件"一般安装好后,会在:</p>
<div class="cnblogs_code">
<pre>/usr/share/applications</pre>
</div>
<p>目录中,但有时我们可能通过wine,arcinstall,生成的快捷桌面菜单等安装的文件不会中"/usr/share/applications"中</p>
<p>而是中:</p>
<div class="cnblogs_code">
<pre>/home/nokia/.local/share/applications</pre>
</div>
<p>删除对应的desktop即可.</p>
<p>&nbsp;</p>
<h1>六.搜狗输入法</h1>
<p><strong>1:乱码</strong></p>
<p>早上启动电脑发现输入中文为乱码</p>
<p><img src="https://images2018.cnblogs.com/blog/540840/201805/540840-20180504092845942-1382449337.png" alt="" width="732" height="64">&nbsp;</p>
<p>解决办法:</p>
<p>不要卸载sougoupinyin,只要在进程里面 "<strong>sougou-qimpanel</strong>"右键kill掉,系统会自动检测sougouPY-daemon并重新激活sougou-qimpanel</p>
<p><img src="https://images2018.cnblogs.com/blog/540840/201805/540840-20180504093210597-1962778891.png" alt="" width="324" height="68"></p>
<p>如果每次重启都显示乱码，那就卸载重新安装吧，*注：在Fcitx配置输入法中，安装的搜狗拼音为“搜狗拼音",自带搜狗拼音为"Sunpinyin",不要搞混了。</p>
<p><strong>2:输入法字体切换显示logo太大</strong></p>
<p>&nbsp;<img src="https://images2018.cnblogs.com/blog/540840/201806/540840-20180614193016056-1296527556.png" alt=""></p>
<p>所以,将原始的active.png (源大小22px*22px) 修改下覆盖<strong>/usr/share/icons/hicolor/48x48/apps/fcitx-sogoupinyin.png</strong> 文件</p>
<p><img src="https://img2018.cnblogs.com/blog/540840/201809/540840-20180920102144496-1559089389.png" alt="">&nbsp;&nbsp;<img src="https://img2018.cnblogs.com/blog/540840/201809/540840-20180920102153270-1597544372.png" alt=""></p>
<p><img src="https://img2018.cnblogs.com/blog/540840/201809/540840-20180920102327613-84984082.gif" alt=""></p>
<h1>七.打开终端每次提示:To run a command as administrator (user "root")....</h1>
<p>每次打开终端提示:</p>
<p>To run a command as administrator (user "root"), use "sudo &lt;command&gt;".<br>See "man sudo_root" for details.</p>
<p>这个表示如果要用管理员操作要用'sudo cmd', 如果要关闭这个提示执行:</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">touch</span> ~/.sudo_as_admin_successful</pre>
</div>
<p>所以出现这个问题的原因是您删除了用户目录下的&nbsp;<em><code><strong>sudo_as_admin_successful&nbsp;</strong></code></em><code> 文件</code></p>
<p>来自:<a href="https://askubuntu.com/a/22614" target="_blank">https://askubuntu.com/a/22614</a>&nbsp;</p>
<p>&nbsp;</p>
<h1>八. Ubuntu的软件源（仓库）&nbsp;</h1>
<p>Launchpad.net 的介绍；&nbsp;</p>
<p>Launchpad is a software collaboration platform that provides:&nbsp;Bug tracking&nbsp;Code hosting using Bazaar&nbsp;Code reviews&nbsp;Ubuntu package building and hosting&nbsp;<em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">Translations&nbsp;</em></em></em></em><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">Mailing lists&nbsp;</em></em></em></em></em></em></em><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">Answer tracking and FAQs&nbsp;</em></em></em></em></em></em></em></em><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">Specification tracking&nbsp;</em></em></em></em></em></em></em></em></em><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel"><em id="__mceDel">Take the tour!</em></em></em></em></em></em></em></em></em></em></p>
<p><em>要找Ubuntu的软件就直接&nbsp;<a title="dcb3688" href="https://launchpad.net/ubuntu" target="_blank">https://launchpad.net/ubuntu</a>&nbsp;&nbsp;</em><strong>Find a Package</strong></p>
<p>如果没有安装软件中心(精简安装)，可以</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">sudo</span> apt <span style="color: #0000ff;">install</span> gnome-software</pre>
</div>
<p>gnome-software 里面的软件均来自launchpad-ubuntu</p>
<p>例如：你可以直接 （deepin的截图工具）</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">sudo</span> apt <span style="color: #0000ff;">install</span> deepin-screenshot</pre>
</div>
<p>也可以search 找到&nbsp;<a title="dcb3688" href="https://launchpad.net/ubuntu/cosmic/amd64/deepin-screenshot" target="_blank">https://launchpad.net/ubuntu/cosmic/amd64/deepin-screenshot</a></p>
<p>&nbsp;同时就能找到二进制的下载地址：&nbsp;<a title="dcb3688" href="http://launchpadlibrarian.net/361451823/deepin-screenshot_4.0.11-1_amd64.deb" target="_blank">http://launchpadlibrarian.net/361451823/deepin-screenshot_4.0.11-1_amd64.deb</a></p>
<p>&nbsp;</p>
<h1>九. 设置Arc-Dark-theme 主题</h1>
<p><strong>Arc Theme</strong></p>
<p>Arc is a flat theme with transparent elements for GTK 3, GTK 2 and GNOME Shell which supports GTK 3 and GTK 2 based desktop environments like GNOME, Unity, Budgie, Pantheon, Xfce, MATE, etc.</p>
<p>相比自带的gnome-theme主题，我更喜欢Arc-Dark主题</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">sudo</span> apt <span style="color: #0000ff;">install</span> arc-theme</pre>
</div>
<p>也可以去&nbsp;<a title="dcb3688" href="https://www.gnome-look.org" target="_blank">https://www.gnome-look.org</a>&nbsp; 找自己喜欢的theme</p>
<p>github地址：&nbsp;<a title="dcb3688" href="https://github.com/horst3180/arc-theme" target="_blank">https://github.com/horst3180/arc-theme</a></p>
<p>安装好之后就能在 外观首选项- 就能看到Arc-Dark</p>
<p><img src="https://images2018.cnblogs.com/blog/540840/201806/540840-20180615103812837-1297073386.png" alt=""></p>
<p>选择自定义，修改窗口边框- Ambiant-MATE</p>
<p><img src="https://images2018.cnblogs.com/blog/540840/201806/540840-20180615103936842-926546321.png" alt=""></p>
<p><strong>修改menu菜单图标</strong></p>
<p>Arc-Dark主题自带的menu图标是debian的logo，更喜欢用Arc-Dark黑色主题图标代替，so，只要替换&nbsp;/usr/share/icons/gnome/@x@/places 其中@x@ 包括&nbsp;16x16、22x22、24x24、32x32、48x48，的<strong>debian-swirl.png</strong>。&nbsp;像素大于23pix才能起作用，因为menu面板Panel最低像素23pix，换句话说，只要修改24、32、48包含的图标也ok。</p>
<p><img src="https://images2018.cnblogs.com/blog/540840/201806/540840-20180623172309957-686730739.png" alt=""><img src="https://images2018.cnblogs.com/blog/540840/201806/540840-20180623172315968-465605502.png" alt=""></p>
<p>&nbsp;</p>
<h1>十.添加快捷键</h1>
<p>在 设置- 控制中心 - 键盘快捷键 中新建</p>
<p>名称：&nbsp;系统监视器</p>
<p>命令：gnome-system-monitor&nbsp;</p>
<p><img src="https://images2018.cnblogs.com/blog/540840/201806/540840-20180615164557912-1974752876.png" alt=""></p>
<p>设置快捷键 ： Shift + Ctrl + Esc</p>
<p>类似的，可以新增DIY自己喜欢的快捷键</p>
<p><span style="background-color: #ffffff; color: #ff0000;">*注: caja必须要带路径&nbsp; （caja /home/nokia/）</span></p>
<p><img src="https://images2018.cnblogs.com/blog/540840/201806/540840-20180615165047511-761618133.png" alt=""></p>
<p>&nbsp;</p>
<h1>十一.xmodmap 修改键盘映射</h1>
<p>查看键盘按键对应的键值：</p>
<div class="cnblogs_code">
<pre>xev | <span style="color: #0000ff;">grep</span> keycode</pre>
</div>
<p>也可以直接打印键值对应编码</p>
<div class="cnblogs_code">
<pre>xmodmap -pke</pre>
</div>
<p>例:将Caps Lock 映射为‘=’&nbsp; （某哥经常用equal）</p>
<p>首先要remove 掉Caps_Lock的Lock，否则即使修改了映射，但Caps_Lock键还保留了Lock的作用，专业一点这叫“<strong>修改修饰键的行为</strong>”</p>
<p>参考：&nbsp;<a href="https://blog.csdn.net/robertsong2004/article/details/36439597" target="_blank">https://blog.csdn.net/robertsong2004/article/details/36439597</a></p>
<p>通过pke查找‘=’与Caps_Lock 对应的keycode</p>
<div class="cnblogs_code">
<pre>keycode <span style="color: #800080;">21</span> =<span style="color: #000000;"> equal plus equal plus
keycode </span><span style="color: #800080;">66</span> = Caps_Lock NoSymbol Caps_Lock</pre>
</div>
<p>在home ~/目录下新建.xmodmaprc文件</p>
<p>内容：</p>
<div class="cnblogs_code">
<pre>remove Lock =<span style="color: #000000;"> Caps_Lock
keycode </span><span style="color: #800080;">66</span> = equal</pre>
</div>
<p>保存后执行：</p>
<div class="cnblogs_code">
<pre>xmodmap ~/.xmodmaprc</pre>
</div>
<p>&nbsp;xmodmap不会自动保存配置，甚至重启fcitx （pkill fcitx &amp;&amp; fcitx）重启系统后便会丢失，所以需要保存到脚本，开机启动</p>
<p>新建脚本<strong>keyboardmaps</strong>&nbsp; 脚本内容</p>
<div class="cnblogs_code">
<pre>#! /bin/<span style="color: #000000;">bash
xmodmap </span>/home/mylinux/.xmodmaprc</pre>
</div>
<p>赋予执行权限</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">chmod</span> <span style="color: #800080;">555</span> keyboardmaps</pre>
</div>
<p>在控制中心-启动应用程序 新增开启启动程序，这里新增的启动程序是用户级别的启动程序，不是系统级别的启动，所以参考 【<a href="#autostart">#autostart</a>&nbsp;锚点】 中的&nbsp;<strong> ~/.config/autostart&nbsp; </strong>目录生成的文件</p>
<p><img src="https://images2018.cnblogs.com/blog/540840/201806/540840-20180622200919817-1290122316.png" alt=""></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<h1>十二.数字小键盘不能用</h1>
<p>数字小键盘Num Lock按压灯有反应，enter也可以用，但其他数字键和运算符号不能用，网上一堆说&nbsp;按下 shift + alt 和 NumLock 或者修改 /etc/console-tools/remap 没一个有效果的，后来找到原因，是因为小键盘接管了鼠标指针</p>
<p>解决办法： <strong>控制中心- 键盘首选项- 鼠标键 去掉 允许使用键盘指针</strong></p>
<p><strong><img src="https://images2018.cnblogs.com/blog/540840/201806/540840-20180622180146141-46253335.png" alt=""></strong></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<h1>十三.Ubuntu开机启动数字小键盘</h1>
<p><strong>方法1：&nbsp;</strong></p>
<p>第一步： 安装numlockx</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">sudo</span> apt-get <span style="color: #0000ff;">install</span> numlockx</pre>
</div>
<p>我们可以直接在终端中使用&nbsp;numlockx on&nbsp; &nbsp;或者&nbsp;numlockx off 看小键盘lock灯的变化。</p>
<p>&nbsp;</p>
<p>第二步:编辑lightdm.conf</p>
<p>为什么编辑的是lightdm.conf 而不是gdm.conf？ 因为从ubuntu11.10开始，gdm被换成了lightdm</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">sudo</span> gedit /etc/lightdm/lightdm.conf</pre>
</div>
<p>加入：</p>
<div class="cnblogs_code">
<pre>greeter-setup-script=/usr/bin/numlockx on</pre>
</div>
<p>重启ok</p>
<p><strong>方法2：</strong></p>
<p>控制中心-登陆窗口管理-设置 激活numlock&nbsp;</p>
<p><img src="https://images2018.cnblogs.com/blog/540840/201806/540840-20180623173517862-1862297042.png" alt=""></p>
<p>&nbsp;</p>
<h1>十四.chrome与Chromium 无法播放flash</h1>
<p>一行代码搞定</p>
<div class="cnblogs_code">
<pre>sudo apt-get install pepperflashplugin-nonfree</pre>
</div>
<p>&nbsp;查看chrome flash细节&nbsp;&nbsp;<strong>chrome://version</strong></p>
<div class="cnblogs_code">
<pre>/usr/lib/chromium-browser/chromium-browser --enable-pinch --ppapi-flash-path=/usr/lib/pepperflashplugin-nonfree/libpepflashplayer.so --ppapi-flash-version=<span style="color: #800080;">30.0</span>.<span style="color: #800080;">0.154</span> --flag-switches-begin --flag-switches-end</pre>
</div>
<p>&nbsp;</p>
<h1>十五.自定义命令</h1>
<p>在~/.bash_aliases 新增</p>
<div class="cnblogs_code">
<pre>alias swapp=<span style="color: #800000;">'</span><span style="color: #800000;">~/.config/swapp.sh</span><span style="color: #800000;">'</span></pre>
</div>
<div class="cnblogs_code">
<pre>alias reswap=<span style="color: #800000;">'</span><span style="color: #800000;">~/.config/reswap.sh</span><span style="color: #800000;">'</span></pre>
</div>
<p>&nbsp;</p>
<p>新增~/.config/swapp.sh, 编辑</p>
<div class="cnblogs_code">
<pre>#!/bin/<span style="color: #0000ff;">sh</span>
<span style="color: #0000ff;">for</span> i <span style="color: #0000ff;">in</span> $( cd /proc;<span style="color: #0000ff;">ls</span> |<span style="color: #0000ff;">grep</span> <span style="color: #800000;">"</span><span style="color: #800000;">^[0-9]</span><span style="color: #800000;">"</span>|<span style="color: #0000ff;">awk</span> <span style="color: #800000;">'</span><span style="color: #800000;"> $0 &gt;100</span><span style="color: #800000;">'</span>) ;<span style="color: #0000ff;">do</span> <span style="color: #0000ff;">awk</span> <span style="color: #800000;">'</span><span style="color: #800000;">/Swap:/{a=a+$2}END{print </span><span style="color: #800000;">'"</span><span style="color: #800000;">$i</span><span style="color: #800000;">"'</span><span style="color: #800000;">,a/1024"M"}</span><span style="color: #800000;">'</span> /proc/$i/smaps <span style="color: #800080;">2</span>&gt;/dev/<span style="color: #0000ff;">null</span> ; <span style="color: #0000ff;">done</span> | <span style="color: #0000ff;">sort</span> -k2nr | <span style="color: #0000ff;">head</span> -<span style="color: #800080;">10</span></pre>
</div>
<p>新增~/.config/reswap.sh, 编辑</p>
<div class="cnblogs_code">
<pre>#!/bin/<span style="color: #0000ff;">sh</span>
<span style="color: #0000ff;">sudo</span> swapoff -a &amp;&amp; <span style="color: #0000ff;">sudo</span>  swapon -a</pre>
</div>
<p>&nbsp;</p>
<p>立即生效</p>
<div class="cnblogs_code">
<pre>source ~/.bashrc</pre>
</div>
<p>在终端输入 swapp,&nbsp; reswap 即可</p>
<p>&nbsp;</p>
<h1>十六.修复desktop</h1>
<p>使用mate桌面环境,安装/卸载某个应用发现desktop不显示,命令行执行caja,显示桌面,但依旧不见面板,once折腾,面板有了,但右侧类似托盘的小程序 "完整指示器小程序"不见了</p>
<p>修复方法: 重新安装mate-desktop</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">sudo</span> apt-<span style="color: #000000;">get update
</span><span style="color: #0000ff;">sudo</span> apt-<span style="color: #000000;">get upgrade
</span><span style="color: #0000ff;">sudo</span> apt-get <span style="color: #0000ff;">install</span> ubuntu-mate-core ubuntu-mate-desktop</pre>
</div>
<p><strong>桌面图标不能自定义排列&nbsp;</strong></p>
<p>习惯了桌面图标拖拽布局,按照自己的意愿排列,左上角显示应用,但某次修改了配置后,桌面图标不能拖拽布局了.</p>
<p>解决办法:</p>
<p><strong>删除~/主目录名/.local文件</strong></p>
<p><img src="https://img2018.cnblogs.com/blog/540840/201810/540840-20181012193011214-1180757466.png" alt=""></p>
<p>&nbsp;</p>
<h1>十七.误操作startx 无法登陆GUI</h1>
<p>一次修改分辨率操作 <strong>sudo startx</strong> 导致分辨率只有640*480像素，重启系统后，输入正确的用户名、密码无法进入系统，一直停留在登陆界面。</p>
<p>解决办法：</p>
<p>修改主目录下的.Xauthority 拥有者组权限，因为执行sudo startx后.Xauthority 归root了，自己登陆的时候没有了权限。</p>
<p>1： 进入root GUI后修改.Xauthority 文件的用户组权限。</p>
<p>2：如果没有root GUI登陆，可以切换到tty1 （ctrl+alt+ F2/F1/...[!7]）输入：</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">sudo</span> <span style="color: #0000ff;">chown</span><span style="color: #000000;"> pcuser:pcuser .Xauthority  # pcuser 用户名
reboot</span></pre>
</div>
<p>&nbsp;</p>
<h1>十八.Nvidia 显卡驱动问题</h1>
<p>显卡驱动有专用驱动与开源驱动，有次从专用驱动切换开源驱动备份系统的时候，一直切换但一直更改不成功。重启后系统小图标就一直提示更新驱动的报错。根据提示执行：&nbsp;<strong>sudo apt install -f</strong> 命令后，显示：</p>
<div class="cnblogs_code">
<pre>正准备解包 .../libnvidia-gl-390_390.<span style="color: #800080;">77</span>-0ubuntu0~gpu18.<span style="color: #800080;">04</span><span style="color: #000000;">.1_i386.deb ...
nvidia</span>-<span style="color: #800080;">340</span> 导致 /usr/lib/i386-linux-gnu/libGL.so.<span style="color: #800080;">1</span> 转移到 /usr/lib/i386-linux-gnu/libGL.so.<span style="color: #800080;">1</span><span style="color: #000000;">.distrib
dpkg</span>-divert: 错误: 删除 被 libnvidia-gl-<span style="color: #800080;">390</span> 转移的 /usr/lib/i386-linux-gnu/libGL.so.<span style="color: #800080;">1</span><span style="color: #000000;"> 时
软件包名不匹配</span></pre>
</div>
<p>解决办法： 移除old 依赖， 参考：<a title="https://askubuntu.com/questions/1035409/installing-nvidia-drivers-on-18-04" href="https://askubuntu.com/questions/1035409/installing-nvidia-drivers-on-18-04" target="_blank">https://askubuntu.com/questions/1035409/installing-nvidia-drivers-on-18-04</a></p>
<p>&nbsp;</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">for</span> FILE <span style="color: #0000ff;">in</span> $(dpkg-divert --list | <span style="color: #0000ff;">grep</span> nvidia-<span style="color: #800080;">340</span> | <span style="color: #0000ff;">awk</span> <span style="color: #800000;">'</span><span style="color: #800000;">{print $3}</span><span style="color: #800000;">'</span>); <span style="color: #0000ff;">do</span> <span style="color: #0000ff;">sudo</span> dpkg-divert --remove $FILE; <span style="color: #0000ff;">done</span>[/CODE]</pre>
</div>
<p>在执行</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">sudo</span> apt <span style="color: #0000ff;">install</span> -f </pre>
</div>
<p>&nbsp;</p>
<h1>十九.删除只读系统文件&nbsp;</h1>
<p>systemback备份系统的时候提示创建以下硬链接时发生了一个错误，指向文件 /var/xdroid/common/rootfs/data/xdroid/xDroidProps.conf&nbsp; ，删除该文件提示</p>
<p><strong>rm: 无法删除'/var/xdroid/common/rootfs/*****': 只读文件系统</strong></p>
<p><strong><img src="https://img2018.cnblogs.com/blog/540840/201810/540840-20181012151814184-2128705756.png" alt="" width="376" height="128"></strong></p>
<p>&nbsp;</p>
<p><strong>解决办法：</strong></p>
<p>查看文件系统与挂载点,卸载文件系统，</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">df</span> -h </pre>
</div>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">sudo</span> <span style="color: #0000ff;">umount</span> -l /dev/loop5   # loop5改为自己的，-l 少不了</pre>
</div>
<p>&nbsp;</p>
<h1>二十.Ubuntu下禁止mysql开机启动</h1>
<p>网上好多是基于redhat系的</p>
<p>&nbsp;chkconfig[--add][--del][--list][<a class="keylink" href="http://www.2cto.com/os/" rel="nofollow" target="_blank">系统</a>服务]或chkconfig[--level&lt;等级代号&gt;][系统服务][on/off/reset]</p>
<p>基于deb系修改办法:修改mysql的&nbsp;<strong>runlevel </strong>的运行等级，找到配置文件不一定是mysql.conf命名。比如lnmp安装的mysql配置文件是/etc/init.d/mysql</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">sudo</span> gedit /etc/init.d/mysql</pre>
</div>
<p>修改Default-Start ，将runlevel 2去掉，关于runlevel 参考：<a href="https://developer.ibm.com/tutorials/l-lpic1-101-3/" target="_blank">https://developer.ibm.com/tutorials/l-lpic1-101-3/</a>&nbsp; &nbsp; &nbsp;， 修改后的片段配置代码如下：</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;"># Comments to support LSB init script conventions
### BEGIN INIT INFO
Provides: mysql
# Required</span>-<span style="color: #000000;">Start: $local_fs $network $remote_fs
# Should</span>-<span style="color: #000000;">Start: ypbind nscd ldap ntpd xntpd
# Required</span>-<span style="color: #000000;">Stop: $local_fs $network $remote_fs
Default</span>-Start:  <span style="color: #800080;">3</span> <span style="color: #800080;">4</span> <span style="color: #800080;">5</span><span style="color: #000000;">
# Default</span>-Stop: <span style="color: #800080;">0</span> <span style="color: #800080;">1</span> <span style="color: #800080;">6</span><span style="color: #000000;">
# Short</span>-<span style="color: #000000;">Description: start and stop MySQL
# Description: MySQL is a very fast and reliable SQL database engine.
### END INIT INFO</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
<h1>二十一.外网ssh访问局域网linux</h1>
<p>1：局域网安装sshd</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">sudo</span> apt <span style="color: #0000ff;">install</span> openssh-server</pre>
</div>
<p>2：设置动态DNS</p>
<p>可以软件安装设置（花生壳、nat123），可以在路由器中设置（系统服务-动态DNS）</p>
<p><img src="https://img2018.cnblogs.com/blog/540840/201811/540840-20181112204402486-1039842884.png" alt=""></p>
<p>3：路由器设置端口映射/ 网络地址转换</p>
<p>传输控制-NAT设置-虚拟服务器（外部端口-对应的内部端口-对应的内部IP）</p>
<p><img src="https://img2018.cnblogs.com/blog/540840/201811/540840-20181112204654526-1812536197.png" alt=""></p>
<p><img src="https://img2018.cnblogs.com/blog/540840/201811/540840-20181112204714084-1482542131.png" alt=""></p>
<p>&nbsp;</p>
<p>4： 外网访问</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">ssh</span> root@<span style="color: #800080;">myhosts99.tpddns.com</span></pre>
</div>
<p>或者Terminal/SecureCRT 直接远程</p>
<p><img src="https://img2018.cnblogs.com/blog/540840/201811/540840-20181112205413676-1073657938.png" alt=""></p>
<p>&nbsp;</p>
<h1>二十二. 安装chromium-browser</h1>
<p>电脑安装chromium-browser 我们可以直接下载官网deb文件或通过命令行安装也可以。但使用linuxdeploy在Android上安装Ubuntu后，下载的deb文件安装提示：</p>
<div class="cnblogs_code">
<pre>dpkg: error processing archive ****.deb (--<span style="color: #0000ff;">install</span><span style="color: #000000;">):
package architecture (amd64) does not match system (armhf)</span></pre>
</div>
<p>所以使用deb安装必须要匹配平台比如，手机Android用的就是arm。</p>
<p><strong>解决办法：</strong></p>
<p>使用通用办法 apt安装，apt会直接匹配安装包的（armhf）平台</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">sudo</span> apt <span style="color: #0000ff;">install</span> chromium-browser</pre>
</div>
<p>&nbsp;</p></div>
