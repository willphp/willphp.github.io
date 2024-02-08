### Helper Functions

#### Cookie

```php
// Check
if (!cookie('?time')) {
    cookie('time', time(), 30); // Set
}
$time = cookie('time'); // Get
echo $time;
cookie('time', null); // Delete
$all = cookie(); // Get all
dump($all);
cookie(null); // Clear all
```

#### Session

```php
if (!session('?time')) {
    session('time', time()); // Set
}
$time = session('time'); // Get
echo $time;
session('time', null); // Delete
$all = session(); // Get all
dump($all);
session(null); // Clear all
```

#### View

```php
public function index()
{
	//return view('index', ['id'=>1]);
	//return view('home/test');	
	//return view()->with('id', 1); //['id'=>1]
	//return view_with(['id'=>1]); //'id',1
	//return view()->with('time', time())->cache(30);
	return view();
}
```

#### URL Generation

```php
echo url('blog/view', ['id'=>1]);
echo url('blog/view?id=1');
echo url('about', [], '.shtml');
echo url('[back]');
echo url('[history]');
```

#### Input Retrieval

```php
echo input('get.id', 1, 'intval');
echo input('post.id', 2, 'intval');
echo input('id');
echo input('pwd', 'a1', 'intval,md5');
echo input('pwd', 'a1', ['intval','md5']);
dump(input('post.'));
dump(input('get.'));
```

#### Form Validation

```php
// Rule: 'form_field', 'validation_rule[|...]', 'error_message[|...]', '[validation_condition]'
$rule = [
    ['email', 'email', 'Email error', AT_MUST]
];
$data = ['email' => 'abc@163'];
// Parameters: 'rule_setting', '[data($_POST)]', '[batch_validation(false)]'
$v=validate($rule, $data);
$v->show(); // Call controller Error::_406($err) when failed
$v->getError(); // Return errors when validation fails, return empty array if passed
$v->isFail(); // Return true if validation fails
```

#### Database

Format:

```
db('table_name',[database_config])->method(arguments)->method();
```
Example:

```php
$list = db('blog')->field('id,title')->select();
```

>This function returns `Db::connect($config, $table)`, with subsequent methods for database operations.

#### Model

```php
// Instantiate app/common/model/Blog.php
$blog = model('common.blog');
// Instantiate app/current_application/model/BlogCate.php
$cate = model('blog_cate'); 
```
>This function returns a singleton instance of the model class, with subsequent methods for model or database operations.

#### Widget

```php
// Instantiate app/common/widget/Blog.php
$blog = widget('common.blog'); 
// Instantiate app/current_application/widget/BlogCate.php
$cate = widget('blog_cate')->get();
```
>This function returns a singleton instance of the widget class, with subsequent methods for the widget class.

#### Configuration

Quick access:

```php
$id = get_config('site.id', 0);//Default 0
```

Integrated functions:

```php
config('site.name', 'php'); //Set
$bool = config('?site.id'); //Check
$all = config(); //Get all
$site = config('site'); //Get site
$id = config('site.id'); //Get site.id 
$id = config('?site.id', 1); //Get, default 1
$id = config('?id', 1, 'site'); //Same as above
$id = site('?id', 1); //Simplified version of site, same as above
```

#### Cache

Get and set:

```php
$id = get_cache('id', fn()=>time(), 10);//Valid for 10 seconds
```

>Default validity period is 0, permanent validity.

Integrated functions:

```php
cache('time', time()); //Set (permanent)
cache('id', 1, 30); //Set (30 seconds)
$bool = cache('?id'); //Check
$id = cache('id'); //Get
cache('name', null); //Delete
cache(null); //Clear
```

#### Debugging

Formatted output:

```php
dump($_SERVER); //Variable
dd([1, 2]); //Output and exit
dump_const(); //User constants
```

Output to debug bar:

```php
trace(['id'=>1], 'debug'); //Default: debug
trace('error info', 'error'); //Error
trace('query sql', 'sql'); //SQL statement
```