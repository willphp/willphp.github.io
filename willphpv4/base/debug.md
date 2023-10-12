### 本地配置

在本地环境时，可重命名`env.example.env`文件为`.env`本地配置文件：

```ini
[APP]
DEBUG = true
TRACE = true
URL_REWRITE = true

[DATABASE]
DEFAULT[DB_TYPE] = mysql
DEFAULT[DB_HOST] = localhost
DEFAULT[DB_NAME] = myapp01db
DEFAULT[DB_USER]= root
DEFAULT[DB_PWD] = 
DEFAULT[DB_PORT] = 3306
DEFAULT[DB_CHARSET] = utf8
DEFAULT[DB_PREFIX] = wp_
```
本地配置默认开启调试模式和调试栏。可设置本地数据库连接信息。

### 变量调试

```php
dump($var1, ...$varN); //用法和var_dump一致
dd($val1); //输出后中止程序的执行
dump_const(); //输出用户常量
trace($var1); //输出变量到调试栏
log_value($var1); //记录变量到日志
```

>在开发过程中出现问题，如修改配置，模板不生效，可尝试清空`runtime`目录。
