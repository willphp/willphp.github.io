### 入口文件

默认入口文件位于`public/index.php`，代码如下：

```php
declare(strict_types=1); //严格模式
namespace willphp\core; 
header('Content-type: text/html; charset=utf-8'); //编码
date_default_timezone_set('PRC'); //时区
define('ROOT_PATH', strtr(realpath(__DIR__ . '/../'), '\\', '/')); //根路径
require ROOT_PATH . '/willphp/bootstrap.php'; //引导文件
App::init()->boot(); //默认绑定index应用(文件名)
```

### 应用绑定

在`App::init()`可设置绑定应用或根据域名绑定应用，如：

```php
//App::init(['admin']) //指定应用admin
//绑定www.a.io admin.a.io api.b.io三个域名来访问三个应用
App::init(['*' => 'index', 'admin' => 'admin', 'api.b.io' => 'api'])
```

>可设置多入口文件来访问应用，如admin.php(代码同index.php)

### URL访问

```
http://serverName/index.php（或其它入口）/控制器/操作/参数/值…
```

### URL重写

隐藏入口文件`index.php`，可以在`config/app.php`中开启url重写：

```
'url_rewrite' => true,
```

Apache环境规则`public/.htaccess`文件：

```
<IfModule mod_rewrite.c>
  Options +FollowSymlinks -Multiviews
  RewriteEngine On
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteRule ^(.*)$ index.php? [L,E=PATH_INFO:$1]
</IfModule>
```

Nginx环境规则`public/nginx.htaccess`文件：

```
location / {
	if (!-e $request_filename) {
		rewrite  ^(.*)$  /index.php/$1  last;
	}
}
```