## Controller Definition

Define controller classes in `app/application_name/controller`, for example:

```php
declare(strict_types=1);
namespace app\index\controller;
class Index
{
	public function index()
	{
	    return 'hello world';
	}
}
```

#### Rendering Output

```php
declare(strict_types=1);
namespace app\index\controller;
class Index
{
	public function index()
	{
	    return view();
	}	
	public function api()
	{
	    return ['code'=>200, 'msg'=>'ok', 'data'=>['id'=>1, 'name'=>'php']];
	}	
}
```

#### Error Handling

Handle errors in `app/application_name/controller/Error.php`, for example:

```php
declare(strict_types=1);
namespace app\index\controller;
class Error
{
    use \willphp\core\Jump;
    // Default handling
    public function __call($method, $args)
    {
        $msg = $args[0] ?? 'An error occurred!';
        $code = str_starts_with($method, '_') ? substr($method, 1) : 400;
        $this->error($msg, (int)$code);
    }
    // Custom controller or method does not exist page
    public function _404(string $msg, array $args = []) {
        dump($msg, $args);
    }
    // Custom accessing non-public method page
    public function _405(string $msg, array $args = []) {
        dump($msg, $args);
    }
    // Custom validation failed page
    public function _406(string $msg, array $args = []) {
        dump($msg, $args);
    }
    // Custom parameter insufficient page
    public function _416(string $msg, array $args = []) {
        dump($msg, $args);
    }
}
```

## Middleware Classes

#### Usage Example

```php
declare(strict_types=1);
namespace app\index\controller;
class Index
{
    protected $middleware = [ 
        'common', // All methods
        'auth' => ['except' => ['login']], // Except login
        'test' => ['only' => ['test']], // Only test
    ]; 
    public function index() {           
        return 'index';
    }
    public function login() {
        return 'login';
    } 
    public function test() {
        return 'test';
    }  
}
```

#### Configuration

Set controller middleware in `config/middleware.php`, for example:

```php
'controller' => [
    'common' => [
        \middleware\controller\Filter::class, // Common
    ],
    'auth' => [
        \middleware\controller\Auth::class, // Authentication
    ],
    'test' => [
        \middleware\controller\Test::class, // Test
    ],
],   
```

#### Middleware Class

Set middleware class in `\middleware\controller\Test.php`, code as follows:

```php
declare(strict_types=1);
namespace middleware\controller;
class Test {	
    public function run($next){	
        trace('Executing test middleware');
        $next();
    }
}
```

## Request Handling

#### Parameter Binding

```php
declare(strict_types=1);
namespace app\index\controller;
class Index
{
    public function index(int $id, array $req, string $name = 'abc')
    {
        dump($id, $name, $req);
    }
}
```

#### Required Parameters

When parameters have no default value (except for `$req`), they must be passed through `$_GET` or `$_POST`, otherwise jump method:

```
Error::_416() // Display: index/index: id parameter is insufficient
```

Correct `GET` request:

```
?id=10&name=php
```

Automatic conversion parameter type, if the id is of type int, passing characters will become 0:

```
?id=abc&name=php // id=0&name=php
```

#### Specific Parameters

`array $req` is a specific parameter of the framework, and its value is the combination of `binding parameters, GET, POST`.

#### Automatic Filtering

You can set automatic filtering processing for `$req` parameters in `config/filter.php`, for example:

```php
'filter_req' => true, // Whether to enable req filtering
'func_html' => 'remove_xss', // html field processing function (multiple functions, separated by commas)
'func_except_html' => 'clear_html', // html except field processing function
// html fields
'html_fields' => ['phtml'], // html field list
'html_like' => 'content', // html field contains
// Field automatic processing
'field_auto' => [
    'id,p' => 'intval',
],
'func_out' => 'stripslashes', // Template output filtering (all)
'func_out_except_html' => 'htmlspecialchars', // Template output filtering (except html)
```

Request example:

```
?id=aa&name=<b>bbb</b>&p=bb
```

Return result:

```
int(0) // id
string(10

) "<b>bbb</b>" // name
array(3) {
  ["id"] => int(0)
  ["name"] => string(3) "bbb"
  ["p"] => int(0)
}
```

## Request Retrieval

> In the controller, it is recommended to use binding parameters to obtain.

```php
input('get.'); // Get $_GET
input('post.'); // Get $_POST
input('get.name'); // Return ''
input('post.cid', 0, 'intval'); // Get intval($_GET['cid'])
input('id', 1, 'intval'); // Get $_POST['id'] or $_GET['id']   
input('get.pwd', '1', ['intval','md5']); // Get md5(intval($_GET['pwd']))   
```

## Control Redirect

You can mount the `\willphp\core\Jump` module to achieve redirect function:

```php
declare(strict_types=1);
namespace app\index\controller;
class Index
{
    use \willphp\core\Jump;
}
```

#### Module Method

```php
success($msg = '', string $url = null) // Success prompt (automatically determine whether to return Json format)
error($msg = '', string $url = null, int $code = 400) // Failure prompt (same as above)
_jump($info, bool $status = false, string $url = null) // Integrated prompt (same as above)
_json(int $code = 200, string $msg = '', array $data = null, array $extend = [])
_url(string $url, int $time = 0) // url redirection
isAjax() // Whether it is ajax submission
isPost() // Whether it is post submission
isGet(): // Whether it is get submission
isPut(): // Whether it is put submission
isDelete(): // Whether it is delete submission
```

> If the application is in the `api_list` list in the `config/app.php` file, the prompt returns in Json format.

#### Usage Example

```php
public function index()
{
    if ($this->isPost()) {
        echo 'isPost';
    }
    return 'isGet';
}
public function msg(int $id = 1)
{
    if ($id == 1) {
        $this->success('Success1', 'index/index');
    } elseif ($id == 2) {
        $this->error('Failure1');
    }
    // Jump integration
    $this->_jump(['Success2', 'Failure2'], ($id == 3), 'index/index');
}
public function json()
{
    $data = ['id'=>1,'name'=>'willphp'];
    $this->_json(200, '', $data, ['debug'=>'test']);
}
```