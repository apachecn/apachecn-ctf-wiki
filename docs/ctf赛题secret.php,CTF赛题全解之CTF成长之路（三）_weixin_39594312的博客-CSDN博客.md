<!--yml
category: 未分类
date: 2022-04-26 14:33:11
-->

# ctf赛题secret.php,CTF赛题全解之CTF成长之路（三）_weixin_39594312的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_39594312/article/details/115649143](https://blog.csdn.net/weixin_39594312/article/details/115649143)

1.题目名称：BuyFlag

打开页面发现有个payflag的页面

![f42371def8d3a1687cccc3f2d1fc43be.png](img/3a338397e79a777289a76b9297a92550.png)

点进去查看源代码发现注释中有代码

![ef2f91233a86a0d476f54d85d3a9d0a2.png](img/7595eb475409423e3188976ba55f92eb.png)

判断password参数是否是404，这里利用了is_numeric函数来进行判断所以提交404%20即可绕过is_numeric的判断。

![aa2ebe150d169280b6dcfecd42e1ad1e.png](img/52e9532777f1d50b9ccbe82e5ad7aded.png)

又要求必须是学生才行，这里看到cookie的user字段。这里我们改为1再进行提交。

通过判断一开始在源代码中的代码，我们添加money参数，将参数设置为flag的价格，然后显示金额太长。

![c234033c62b2ec094ac4f80b5f65f10c.png](img/2dd794e9e705237627d7fdd512f2f2a5.png)

在经过测试后发现修改为数组后可以获得flag

![3ff6810040c2188f874cd1d6267a441a.png](img/bc630d9200db054c3e683969fc4880a9.png)

2.题目名称：你传你马呢

打开主页发现文件上传的地方

![73a27a2a5db1054c9f72003d66cf2a29.png](img/6fdd9489e7dbad9fa437be467fc925f3.png)

上传一个写有后门的图片格式文件发现可以上传。

![5a9ab3579603dda240179a60e576786b.png](img/8e81652dd841c8a3c6ea5cd0236fa827.png)

我们上传一个.ph作为尝试，发现已经被拦截，然后进行修改大小写发现依然拦截。所以推测是对ph进行了检测进制上传。

![65d7fd597a7f628137693edecdf7bfc7.png](img/3beb7e19c11e88ae67b9230cbff97eec.png)

那么我们就尝试上传一个.htaccess，发现可以上传。

![3255bd0fffe5c9d4fd975decd12d01dc.png](img/752acbcc8f7e0b81c31d6ade430d7913.png)

接下来直接上传一个jpg格式的带有后门的文件。

![1487ab232a49028a2051cb5b61afc7e7.png](img/84911fffedf12ae995677fa74fe59198.png)

菜刀连接，获得flag。

![da008f5a9d52ac5c6cec1e86ead17366.png](img/137dcfc2c905fa3e52fa251785bf2dd5.png)

3.题目名称：BackupFile

通过提示下载index.php.bak

![e0fc90fa0910fde9df7680085f844e8f.png](img/2e9463e96c9d00437c1e57dcc96398c5.png)

打开文件发现如下代码

include_once "flag.php";

if(isset($_GET['key'])) {

$key = $_GET['key'];

if(!is_numeric($key)) {

exit("Just num!");

}

$key = intval($key);

$str = "123ffwsfwefwf24r2f32ir23jrw923rskfjwtsw54w3";

if($key == $str) {

echo $flag;

}

}

else {

echo "Try to find out source file!";

}

Key只能为一个整数，这里我们通过php的弱类型比较，直接将传入123即可获得flag

![627878aa014a98feaeba0dea01cbf71e.png](img/9c2e086b958bda8ea7ffbaab237f199e.png)

4.题目名称：easy_tornado

打开题目显示了3个文件

![1fbc46d7e2d957eb58f039aa187570d3.png](img/8d287387bacb57f0bb3e3073ca6467e1.png)

打开后发现连接中每个文件后面都会有一个filehash。

![dd6d050555d96f4ed1e2c676900d328e.png](img/91e05f71f515463699a6f9cfd3eaf344.png)

尝试修改文件名发现包含不了其它文件。提示Error

![83619e04a1d73ca7d13a0c3583da2392.png](img/1a5218ca356fe81c9a735b82ebd142b4.png)

其实这个页面是存在注入点的，将参数修改为msg={{handler.settings}}可看到cookie_secret

![4f21f30148a6faa8c3abea7eb2e4da88.png](img/4474d54044d8e7930c2a3d6b2e413c06.png)

根据初始三个文件内所提示的flag文件为/fllllllllllllag，filehash的算法为md5(cookie_secret+md5(filename))

对应的脚本为：

echo md5('ca906815-a3dc-4636-ad24-057f7b90fc2f'.md5('/fllllllllllllag'));

读取flag：

![28afda793bc90c5b23c84af4138a1fd2.png](img/82ff5da4c511bbcbd75c282af5ad05f6.png)