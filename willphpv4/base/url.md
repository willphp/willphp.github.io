### Entry File

The default entry file is located at `public/index.php`, and the code is as follows:

```php
declare(strict_types=1); // Strict mode
namespace willphp\core; 
header('Content-type: text/html; charset=utf-8'); // Encoding
date_default_timezone_set('PRC'); // Timezone
define('ROOT_PATH', strtr(realpath(__DIR__ . '/../'), '\\', '/')); // Root path
require ROOT_PATH . '/willphp/bootstrap.php'; // Bootstrap file
App::init()->boot(); // Default binding to the index application (file name)
```

### Application Binding

In `App::init()`, you can set the binding application or bind the application based on the domain name, such as:

```php
// App::init(['admin']) // Specify the admin application
// Bind three domains www.a.io admin.a.io api.b.io to access three applications
App::init(['*' => 'index', 'admin' => 'admin', 'api.b.io' => 'api'])
```

> You can set multiple entry files to access applications, such as admin.php (same code as index.php).

### URL Access

```
http://serverName/index.php（or other entry）/Controller/Action/Parameter/Value…
```

### URL Rewrite

To hide the entry file `index.php`, you can enable URL rewrite in `config/app.php`:

```
'url_rewrite' => true,
```

Apache environment rules in `public/.htaccess` file:

```
<IfModule mod_rewrite.c>
  Options +FollowSymlinks -Multiviews
  RewriteEngine On
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteRule ^(.*)$ index.php? [L,E=PATH_INFO:$1]
</IfModule>
```

Nginx environment rules in `public/nginx.htaccess` file:

```
location / {
	if (!-e $request_filename) {
		rewrite  ^(.*)$  /index.php/$1  last;
	}
}
```