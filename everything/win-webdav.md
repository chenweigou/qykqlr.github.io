<a name="hHECl"></a>
# WebDAV
WebDAV （Web-based Distributed Authoring and Versioning） 一种基于 HTTP 1.1协议的通信协议。它扩展了HTTP 1.1，在GET、POST、HEAD等几个HTTP标准方法以外添加了一些新的方法，使应用程序可对Web Server直接读写，并支持写文件锁定(Locking)及解锁(Unlock)，还可以支持文件的版本控制。  <br />简单说，WebDAV也是http(https)协议的链接，但是可以添加到文件系统，当成一个硬盘来使用。<br />介绍两种Windows连接WebDAV的方式，推荐RaiDrive，可以直接播放网络硬盘里的视频
<a name="H72sd"></a>
# 使用Windows系统功能连接WebDAV

1. 在“我的电脑”上点击鼠标右键， 选择“映射网络驱动器”
2. 在弹出的窗口里选择驱动器号，文件夹填写链接，可以指定子文件夹

![图片.png](https://cdn.nlark.com/yuque/0/2023/png/12831520/1675947206750-c9ceaf73-88fe-47c7-909e-05417db5d0cc.png#averageHue=%23fbfafa&clientId=uaef943f3-9e48-4&from=paste&height=335&id=u8f8de0f1&name=%E5%9B%BE%E7%89%87.png&originHeight=460&originWidth=612&originalType=binary&ratio=1&rotation=0&showTitle=false&size=25208&status=done&style=none&taskId=uf6a12e6c-2d07-4a25-9acd-ea504e464e6&title=&width=446)

3. 继续输入凭据

![图片.png](https://cdn.nlark.com/yuque/0/2023/png/12831520/1675947453932-6b9d1132-e514-49e8-b0a6-ddd400a89cd4.png#averageHue=%23f9f9f9&clientId=uaef943f3-9e48-4&from=paste&height=321&id=u90df267b&name=%E5%9B%BE%E7%89%87.png&originHeight=353&originWidth=456&originalType=binary&ratio=1&rotation=0&showTitle=false&size=16035&status=done&style=none&taskId=u7bbd1734-8632-4e8e-8718-22e9e73ab65&title=&width=415)

4. 成功连接后就会变成一个“我的电脑”下的一个磁盘，文件路径和本地硬盘一样

![图片.png](https://cdn.nlark.com/yuque/0/2023/png/12831520/1675947521471-dc0ecbbb-bbdc-4f7b-8906-3e2fcf814128.png#averageHue=%23fefdfd&clientId=uaef943f3-9e48-4&from=paste&height=80&id=uc71e266f&name=%E5%9B%BE%E7%89%87.png&originHeight=115&originWidth=678&originalType=binary&ratio=1&rotation=0&showTitle=false&size=6417&status=done&style=none&taskId=u6631e495-055a-46dc-91c9-fcb3c5dc9a5&title=&width=473)
<a name="zLAEw"></a>
## Windows下连接http协议的WebDAV出错
 Windows的WebDAV默认只支持https协议，如果添加http协议的WebDAV出错需要修改注册表

1. 同时按下键盘的“Win+R”键弹出运行窗口，输入 “regedit”选确定打开注册表 

![图片.png](https://cdn.nlark.com/yuque/0/2023/png/12831520/1675947947397-2d95e78c-1183-46ac-aed9-9fa70a79657b.png#averageHue=%23f6f6f4&clientId=uaef943f3-9e48-4&from=paste&height=226&id=udca01517&name=%E5%9B%BE%E7%89%87.png&originHeight=226&originWidth=394&originalType=binary&ratio=1&rotation=0&showTitle=false&size=12549&status=done&style=none&taskId=u158078af-0611-4115-ab9d-dea791d31b2&title=&width=394)

2. 修改之前可以选择“文件->导出”将旧的配置备份
3. 在注册表的地址栏输入下面地址
>   计算机\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WebClient\Parameters

4. 修改【BasicAuthLevel】的值为【2】，表示同时支持https和http两种协议

![图片.png](https://cdn.nlark.com/yuque/0/2023/png/12831520/1675949672084-9882a8fd-e815-4d9f-b6aa-980cc33e4295.png#averageHue=%23f9f5f2&clientId=u426d6f73-dea8-4&from=paste&height=274&id=u37cb6efd&name=%E5%9B%BE%E7%89%87.png&originHeight=423&originWidth=948&originalType=binary&ratio=1&rotation=0&showTitle=false&size=63125&status=done&style=none&taskId=ude5a7242-04aa-4be1-89ad-9009f62f670&title=&width=613)<br />![图片.png](https://cdn.nlark.com/yuque/0/2023/png/12831520/1675948173803-8f20d101-7175-46a8-9473-6a59a713f470.png#averageHue=%23edeceb&clientId=uaef943f3-9e48-4&from=paste&height=229&id=u07f677ff&name=%E5%9B%BE%E7%89%87.png&originHeight=249&originWidth=379&originalType=binary&ratio=1&rotation=0&showTitle=false&size=11321&status=done&style=none&taskId=u71487ef6-c4b1-4cff-91e9-73bbb63d360&title=&width=349)

<a name="jfmj3"></a>
# 使用RaiDrive连接WebDAV
如果Windows自带的WebDAV工具不好用，或者连接不了， 可以尝试使用[RaiDrive](https://www.raidrive.com/)

1. 到RaiDrive官网下载软件，安装后打开，点击顶部“添加”

![图片.png](https://cdn.nlark.com/yuque/0/2023/png/12831520/1675948653151-b21d4ef6-04dc-49af-99c7-5c81b000cabc.png#averageHue=%23333232&clientId=u9804deac-cff4-4&from=paste&height=181&id=u17ee1080&name=%E5%9B%BE%E7%89%87.png&originHeight=181&originWidth=734&originalType=binary&ratio=1&rotation=0&showTitle=false&size=14396&status=done&style=none&taskId=ua11ff4c8-029b-42a5-95e0-9eb3005e46a&title=&width=734)

2. 在弹出的窗口选择“NAS->WebDAV”,输入配置， 这里如果选择“在连接时输入密码”，则需要在每次连接都要重新输入凭据，以及“地址”后面的选择框是决定地址是否https的

![图片.png](https://cdn.nlark.com/yuque/0/2023/png/12831520/1675949070497-e5eb2cfd-be8b-4a1c-b2c7-67102ef1ff17.png#averageHue=%23323232&clientId=u9804deac-cff4-4&from=paste&height=539&id=ucc0e6be1&name=%E5%9B%BE%E7%89%87.png&originHeight=640&originWidth=729&originalType=binary&ratio=1&rotation=0&showTitle=false&size=63997&status=done&style=none&taskId=u103b1871-b725-4bd6-8a75-7152900f0c7&title=&width=614)

3. 下图是RaiDrive和Windows工具连接后的情况，虽然RaiDrive可以修改驱动器名， 但实际使用地址都是卷标，所以影响不大

![图片.png](https://cdn.nlark.com/yuque/0/2023/png/12831520/1675948592457-9c293ff4-6ce5-4b55-8add-a84ffc9074d4.png#averageHue=%23f8f7f6&clientId=u9804deac-cff4-4&from=paste&height=101&id=ub8dcc404&name=%E5%9B%BE%E7%89%87.png&originHeight=101&originWidth=571&originalType=binary&ratio=1&rotation=0&showTitle=false&size=10598&status=done&style=none&taskId=u3f816fae-2f7a-4588-8f32-7bd3100d922&title=&width=571)

另外RaiDrive也支持添加其他类型的网络驱动器，包括同步部分云网盘，可以自行研究
