title: 基于CodeIgniter框架Restful风格API的Auth验证
tags:
- Web
- Restful
---
服务端开始准备改造成Restuful风格API.
旧版本采用if else对请求判断分发的机制太丑陋，
选用了CodeIgniter框架。

使用插件：
https://github.com/chriskacerguis/codeigniter-restserver

因为使用了CodeIgniter的SAE版本，避免配置破坏，所以只复制插件目录下的三个文件。

    config/rest.php
    libraries/Format.php
    libraries/Rest_Controller.php

根据官方示例编码自己的RestController。
##### 修改Auth配置 #####
使用Digest认证
http://zh.wikipedia.org/wiki/HTTP摘要认证
```php
    $config['rest_realm'] = 'REST API'; //定义realm用于加密
    $config['rest_auth'] = 'digest';  //切换auth规则方案为digest
    $config['auth_source'] = 'library'; //切换验证方案为自定义验证
    $config['auth_library_class'] = 'auth'; //定义用于执行验证的类名
    $config['auth_library_function'] = 'login'; //定义用于验证的方法明
```

配置完参数后，该框架讲使用libraries文件夹下面的auth类名，通过login方法进行验证请求是否有效。
login方法必须含有两个参数
并且在digest认证下，必须返回md5(username:realm:password)组合后的字符串，框架会自行对请求中的参数头进行验证是否与该串一致。
例如如下代码将通过用户名admin与密码1234的用户通过验证。

    <?php
    class auth
    {
        public function login($username, $password)
        {
            return md5('admin' . ':' . "REST API" . ":1234");
        }
    }

如果使用Basic认证，只需要将rest_auth配置切换成basic并且在login中返回true、false对应成功与失败即可。
