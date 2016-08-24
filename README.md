#hikvision-control
本demo主要实现hikvision网络摄像机的登录、云台旋转控制、实时图片截取功能。

##库文件安装
将src-dll文件夹中的文件拷贝到系统的库文件目录，主意文件名称不要进行修改。

windows7，windows server目录如下：
```
C:\Windows\System32
```
当然你也可以自己定义库文件加载的路径，不过就需要修改`com.hxh.hikvision.api.HCNetSDK.java`中的如下代码：
```
HCNetSDK INSTANCE = (HCNetSDK) Native.loadLibrary("path/HCNetSDK",HCNetSDK.class);
```
其中path为HCNetSDK库文件的根目录绝对路径地址。

##如何使用
将src-lib中的jar拷贝到项目lib目录中，将com.hxh.hikvision.api拷贝到项目源代码目录中。

示例代码如下：
```
// 创建登录对象
LoginPlay lp = new LoginPlay();
// 输入摄像机ip，端口，账户，密码登录
lp.doLogin("218.206.13.27", (short)8000, "admin", "12345");
	
// 截取摄像机实时图片
Control.getImgSavePath("218.206.13.27", "C://img/2.jpg");
// 制摄像机云台控制(开启)
Control.cloudControl("218.206.13.27", CloudCode.PAN_LEFT, CloudCode.SPEED_LV6, CloudCode.START);
try {
	// 让云台运行1000ms
	Thread.sleep(1000);
} catch (InterruptedException e) {
	e.printStackTrace();
}
// 制摄像机云台控制(关闭)
Control.cloudControl("218.206.13.27", CloudCode.PAN_LEFT, CloudCode.SPEED_LV6, CloudCode.END);
```
##api说明
####LoginPlay
```
boolean doLogin(String ip, long port, String username, String password)
```
登录到摄像机，登录成功后将会缓存该摄像机登录对象。后续的所有操作都必须建立在该摄像机已经登陆的前提下才能正确完成。

####Control
```
boolean getImgSavePath(String ip, String savepath)
```
截取摄像机实时画面，ip为已经登陆的摄像机ip地址，savepath截取图片保存的具体目录位置。

```
boolen cloudControl(String ip, CloudCode code1, CloudCode code2, CloudCode code3,)
```
摄像机云台控制，ip为已经登陆的摄像机ip地址，code1为云台控制方向，code2为云台运行速度，code3为控制是否为停止操作。

##异常处理
我在 windows server 64 位系统中运行出现 com.sun.jna.Native 无法加载 HCNetSDK 库文件的异常。

经过各种实验排查，最后发现是因为系统安装的 JDK 版本为 64 位版本，将 JDK 替换为 32 为版本后就一切正常了。
