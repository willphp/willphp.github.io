## Route Configuration

You can configure route settings in `config/route.php`, as follows:

```php
'default_controller' => 'index', // Default controller
'default_action' => 'index', // Default action
'get_var' => 's', // $_GET variable for path_info
'check_regex' => '#^[a-zA-Z0-9\x7f-\xff\%\/\.\-_]+$#', // Route path_info validation regex
'url_suffix' => '.html', // Automatically add suffix in url function
'clear_suffix' => ['.html'], // Automatically clear suffix in route parsing
'get_filter_empty' => false, // Filter empty values and 0 in $_GET variables
// Route setting regex alias
'alias' => [
    ':num' => '[0-9\-]+', // Number
    ':float' => '[0-9\.\-]+', // Float
    ':string' => '[a-zA-Z0-9\-_]+', // \w
    ':alpha' => '[a-zA-Z\x7f-\xff0-9-_]+', // Including Chinese characters
    ':page' => '[0-9]+', // Page number
    ':any' => '.*', // Any
],
```

## Application Routes

Route rules are set in `route/application_name.php`, like:

```php  
// Format: 'Route (expression)' => 'Controller/method/[parameter/match value]'
'index' => 'index/index',
'index_p(:num)' => 'index/index/p/${1}',
'cid(:num)_p(:num)' => 'index/index/cid/${1}p/${2}',
```

## URL Generation

The `url` function generates the corresponding URL link based on the current application route settings. The format is as follows:

```php
url('Controller/method', [parameters], [suffix]);    
```

For example:

```php
echo url('index/index'); // index.html 
// Prepend @ to bypass route
echo url('@index/index'); // index/index.html 
// If controller is not set, defaults to the current controller
echo url('test'); // index/test.html 
// test/index
echo url('test/index'); // test/index.html 
// index/index/p/1 
echo url('index?p=1'); // index_p1.html   
// index/index/p/2
echo url('index', ['p'=>2], '.php'); // index_p2.php 
echo url('index',['cid'=>1, 'p'=>2]); // cid1_p2.html
```
     