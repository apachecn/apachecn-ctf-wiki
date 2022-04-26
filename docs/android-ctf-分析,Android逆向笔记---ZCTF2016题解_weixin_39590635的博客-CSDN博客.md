<!--yml
category: 未分类
date: 2022-04-26 14:37:10
-->

# android ctf 分析,Android逆向笔记 - ZCTF2016题解_weixin_39590635的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_39590635/article/details/117584804](https://blog.csdn.net/weixin_39590635/article/details/117584804)

这是2016年zctf的一道Android题目，样本和本文用到的部分工具在文章末尾可以下载

0x01 第一部分 静态分析

安装运行apk，需要输入用户名和密码，用户名为zctf，用jeb工具反编译

文件结构如下：

![bc78deda5c97](img/beed456bbb7ea718840753415f5d5e10.png)

图1

如图 assets目录中有三个文件，bottom、flag.bin、key.db，还有一个JNIclass库。

![bc78deda5c97](img/beed456bbb7ea718840753415f5d5e10.png)

图2

程序开始，先检查密码的长度(至少4位)，然后调用auth方法验证用户名密码是否正确，如果正确则执行OpenNewActivity方法

auth方法的第二个参数是用户名密码的组合，第三个为另一个方法databaseopt()，看名字应该是读取数据库，看下这个方法：

![bc78deda5c97](img/beed456bbb7ea718840753415f5d5e10.png)

图3

注：关于查找2131099679对应的字符串

先把数字转换成16进制：0x7F06001F，然后在public.xml中找到对应索引，在去strings.xml中查看字符串

先把key.db从assets目录拷贝到应用目录中，然后调用sql语句查询id为0对应字段的值

打开数据库查到key为zctf2016

继续看auth的实现：

![bc78deda5c97](img/beed456bbb7ea718840753415f5d5e10.png)

图4

先将用户名密码组成的字符串反转，利用前面的key加密，然后和assets中的flag_bin做比较，如果相等就返回1

看下encrypt方法看到是DES加密，而且还提供了解密方法，所以可以直接解密出用户名密码：

![bc78deda5c97](img/beed456bbb7ea718840753415f5d5e10.png)

图5

java代码解密：

public static void main(String[] args) {

// TODO Auto-generated method stub

try {

byte[] buffer = readbytes("flag.bin");

System.out.println(buffer.length);

byte[] result = decrypt(buffer, "zctf2016");

System.out.println(new StringBuffer(new String(result)).reverse()

.toString());

} catch (Exception e) {

e.printStackTrace();

}

}

public static byte[] readbytes(String filedir) throws IOException {

FileInputStream fis = new FileInputStream(new File(filedir));

ByteArrayOutputStream baos = new ByteArrayOutputStream();

byte[] cache = new byte[1024];

int len = 0;

while ((len = fis.read(cache)) != -1) {

baos.write(cache, 0, len);// 因为一般最后一次read不能装满cache,用这种方式确保不会将cache末尾空数据写入数据流中

}

fis.close();

baos.close();

return baos.toByteArray();

}

运行打印输出:

16

zctf{Notthis}

所以密码为{Notthis}

继续往下看OpenNewActivity方法：

![bc78deda5c97](img/beed456bbb7ea718840753415f5d5e10.png)

图6

打开了一个新的activity，把密码也传过去了

看app.class的内容：

![bc78deda5c97](img/beed456bbb7ea718840753415f5d5e10.png)

图7

Activity启动后先接收到传过来的密码，然后调用CheckOperatorNameAndroid方法检测调试器，接着调用了dataProvider.add方法(native方法)，如果返回结果为1就退出，如果不为1则调用pushthebottom和dataProvider.sayHelloInc。最终的flag应该就藏在sayHelloInc中

再仔细分析，先看CheckOperatorNameAndroid中启动的定时任务this.task：

![bc78deda5c97](img/beed456bbb7ea718840753415f5d5e10.png)

图8

获得当前activity的taskid，如果不为0就退出。(当前activity的taskid肯定不为0，所以一定会退出)

add方法是native方法，实现在libJNIclass.so中，用IDA打开F5查看

![bc78deda5c97](img/beed456bbb7ea718840753415f5d5e10.png)

图9

打开/proc/pid/status，读取每一条信息到v5和v4中，计算v4的值进行反调试检测

pushthebottom方法是将assets中的bottom文件拷贝到应用安装目录中：

![bc78deda5c97](img/beed456bbb7ea718840753415f5d5e10.png)

图10

最后看下native方法sayHelloInc，用IDA打开查看

sayHelloInc只有1个String参数，修改前两个参数的类型为JavaVM和JNIEnv

![bc78deda5c97](img/beed456bbb7ea718840753415f5d5e10.png)

图11

这里调用了sub_14B0函数，其实就是上面的add调用的函数，用来反调试。然后就是打开bottom文件读到内存中，调用DES_Decrypt解密数据到v24中，接着free释放内存，所以需要在这个地方把解密的数据拷贝出来。

应用执行的整个流程如下：

![bc78deda5c97](img/beed456bbb7ea718840753415f5d5e10.png)

图12

0x02 第二部分 动态调试

前面的就是整个程序的静态分析的过程，接下来需要从程序中拿到flag。

用户名密码已经通过解密算法计算出来了，还需要解决的问题有：

定时任务结束程序、add反调试和sub_14B0反调试。

在定时任务中先判断getTaskId()是否为0，如果不为0就退出程序，解决方法主要有两种：

i. 反编译修改smali代码的if语句绕过

ii. 用hook修改getTaskId的返回值

下面用xposed hook的方式修改getTaskId和add的返回值绕过干扰，相关代码：

public class HookGetTaskId implements IXposedHookLoadPackage {

@Override

public void handleLoadPackage(XC_LoadPackage.LoadPackageParam loadPackageParam) throws Throwable {

if (!loadPackageParam.packageName.equals("com.zctf.app")) return;

findAndHookMethod("android.app.Activity", loadPackageParam.classLoader, "getTaskId", new XC_MethodReplacement() {

@Override

protected Object replaceHookedMethod(MethodHookParam methodHookParam) throws Throwable {

return 0;

}

});

findAndHookMethod("com.zctf.app.JNIclass", loadPackageParam.classLoader, "add", int.class, int.class, new XC_MethodReplacement() {

@Override

protected Object replaceHookedMethod(MethodHookParam methodHookParam) throws Throwable {

return 0;

}

});

// findAndHookMethod("android.app.Activity", loadPackageParam.classLoader, "getTaskId", new XC_MethodHook() {

//

//

// @Override

// protected void beforeHookedMethod(MethodHookParam param) throws Throwable {

// XposedBridge.log("before gettaskid:" + param.getResult());

// param.setResult(0);

// }

//

// @Override

// protected void afterHookedMethod(MethodHookParam param) throws Throwable {

// XposedBridge.log("after gettaskid:" + param.getResult());

// param.setResult(0);

// }

// });

}

}

用replaceHookedMethod更省事，直接替换方法内容，修改返回值也行。

安装xposed，重启手机，运行apk就可以正常进入到sayHelloInc函数中，接下来就需要动态调试从内存中找flag。

在dvm.so中的open函数上下断点，让程序可以在加载jniclass.so的时候停下来，运行程序输入用户名密码后，so文件加载到内存中，然后在sayHelloInc处下断点，调试。

![bc78deda5c97](img/beed456bbb7ea718840753415f5d5e10.png)

图13

F8单步运行到反调试的地方，然后修改返回值R0为0，绕过反调试

![bc78deda5c97](img/beed456bbb7ea718840753415f5d5e10.png)

图14

单步运行结合F5查看伪代码调试，直到解密出数据到内存中：

![bc78deda5c97](img/beed456bbb7ea718840753415f5d5e10.png)

图15

![bc78deda5c97](img/beed456bbb7ea718840753415f5d5e10.png)

图16

DES_Decrypt函数的第一个参数存的是解密前的数据，第二个参数是数据的大小对应R11+0x100为0x1338，第三个参数是解密的密钥对应R8为{Notthis，第四个参数存的是解密后的数据对应R7，v23即0x77f9d008地址开始的就是解密后的数据，看到是png格式，在hex中选中数据保存为d.png

或者使用脚本导出：

auto fp,addr_start,addr_now,size,addr_end;

addr_start = R7;

size = 0x1338;

fp = fopen("d:\d.png","wb");

for(addr_now=addr_start;addr_now

fputc(Byte(addr_now),fp);

到导出后打开图片发现没有flag，用stegsolve打开按下边的’>’分层查看，最后在Red Plane层找到flag

![bc78deda5c97](img/beed456bbb7ea718840753415f5d5e10.png)

图17

apk样本和stegsolve工具下载：http://pan.baidu.com/s/1nvHOOJZ