### Local Configuration

During local development, you can rename the `env.example.env` file to `.env` as the local configuration file:

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

The local configuration enables debug mode and debug bar by default. You can configure the local database connection information.

### Variable Debugging

```php
dump($var1, ...$varN); // Usage is the same as var_dump
dd($val1); // Output and halt program execution
dump_const(); // Output user-defined constants
trace($var1); // Output variables to debug bar
log_value($var1); // Log variables to the log
```

>If you encounter issues during development, such as configuration changes or template issues, try clearing the `runtime` directory.