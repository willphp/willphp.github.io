## Model Definition

Define model classes in `app/application_name/model` as follows:

```php
declare(strict_types=1);
namespace app\index\model;
use willphp\core\Model;

class Cate extends Model
{
    protected string $table = 'cate'; // Table name
    protected string $pk = 'id'; // Primary key
    
    // Automatically format time (create_time field)
    public function getCreateTimeAttr($val, array $data) {
        return date('Y-m-d H:i', $val);
    }
}
```

#### Get Model

```php
$cate = model('index.cate'); // Access across applications
$cate = model('cate')->find(1); // Get the current model object 
dump($cate->toArray()); // Convert object to array 
```

## Model Attributes

```php
protected string $dbConfig = ''; // Database connection configuration
protected array $allowFill = ['*']; // Allow fillable fields
protected array $denyFill = []; // Disallow fillable fields
// Automatically write time types: int|date|datetime|timestamp
protected string $autoTimeType = 'int'; // Do not auto-write when empty
protected string $createTime = 'create_time'; // Create time field
protected string $updateTime = 'update_time'; // Update time field
protected bool $isBatch = false; // Batch validation flag
protected array $validate = []; // Auto validation rules
protected array $auto = []; // Auto processing rules
protected array $filter = []; // Auto field filtering
protected string $showError = 'show'; // Error response method: show|redirect
```

## Before and After Methods

You can set methods before and after model insertion, update, and deletion:

```php
// Before methods
protected function _before_insert(array &$data): void {}
protected function _before_update(array &$data): void {}
protected function _before_delete(array $data): void {}
// After methods
protected function _after_insert(array $data): void {}
protected function _after_update(array $old, array $new): void {}
protected function _after_delete(array $data): void {}
```

Example of usage:

```php
protected function _before_insert(array &$data): void 
{
    // Validate data or perform other operations
    if ($data['cid'] == 0) {
        $this->errors['cid'] = 'Please select a category';
        return;
    }
    $data['status'] = 1; // Auto-fill field
}
```

## Constant Explanation

When setting `auto validation`, `auto processing`, and `auto filtering` for models, you need to use the following constants or values:

```php
// Condition constants
const AT_MUST = 1; // Must
const AT_NOT_NULL = 2; // Not null
const AT_NULL = 3; // Null
const AT_SET = 4; // Has field
const AT_NOT_SET = 5; // No field
// Timing constants
const IN_BOTH = 1; // All operations
const IN_INSERT = 2; // Insert
const IN_UPDATE = 3; // Update
```

## Auto Validation

Validation rule setting is the same as `form validation`, with multiple `timing constants` parameters, as shown below:

```php
// Format: 'field', 'validation rule[|...]', 'error message[|...]', [condition constant], [timing constant]
protected array $validate = [    
    ['username', 'required|unique', 'User required|User already exists', AT_MUST, IN_INSERT],
    ['password', '/^\w{6,12}$/', 'Password must be 6-12 characters', 1, 2],  
    ['repassword', 'confirm:password', 'Passwords do not match', 1, 2],    
    ['iq', 'checkIq', 'IQ must be greater than 100', 1, 1],
    ['email', 'email', 'Invalid email format', AT_NOT_NULL, IN_BOTH],             
];
// Custom validation rule
public function checkIq($value, string $field, string $params, array $data): bool
{
    return $value > 100;
}
```

## Auto Processing

Processing methods:

```
string      // Fill (default)
function    // Function
method      // Custom method
```

Example code:

```php
// Format: 'field', 'processing rule', 'processing method', [condition constant], [timing constant]
protected array $auto = [
    ['password', 'setPwd', 'method', AT_NOT_NULL, IN_BOTH],    
    ['status', '1', 'string', AT_MUST, IN_INSERT],          
    ['add_time', 'time', 'function', 1, 2],              
];
// Custom processing rule
public function setPwd(string $val, array $data): string
{
    return md5($val);
}
```

## Auto Filtering

```php
// Format: 'field', [condition constant], [timing constant]
protected array $filter = [
    ['password', AT_NULL, IN_UPDATE], // Filter when empty and updating
];
```

## Model Usage

Models trigger `auto validation`, `auto processing`, and `auto filtering` when inserting and modifying data.

#### Model Insertion

```php
$cate = model('cate');
$cate['cname'] = 'cat';
$cate['sort'] = 1;
$cate->save();

// Or:
model('cate')->save(['cname'=>'cat', 'sort'=> 2]);
```

#### Model Modification

```php
$cate = model('cate')->find(1);
$cate['cname'] = 'dog';
$cate['sort'] = 2;
$cate->save();

// Or:
model('cate')->save(['id'=>1, 'cname'=>'cat', 'sort'=> 2]);
```

#### Model Deletion

```php
$cate = model('cate')->find(1);
$cate->del();

// Or:
model('cate')->del(1);
```

#### Error Response

You can set the default response method for auto-validation failure in the save method, such as:

```php
protected string $showError = 'show'; // Default: Auto redirect to Error::_406 method
protected string $showError = 'redirect'; // Auto redirect to the source page
protected string $showError = ''; // Do not redirect
```
When not redirecting, get the error:

```php
$blog = model('blog')->find(1);
$blog['cid'] = 0;
$blog->save();
if ($blog->isFail()) {
    dump($blog->getError()); // ['cid'=>'Please select a category']
}
```

#### Other Methods

You can use all methods in the `database table` with the model, such as:

```php
$cate = model('cate');
$cate->getTable();  // Get model table name
$cate->getPk();     // Get table primary key
$cate->getPrefix(); // Get table prefix
$cate->where('status', 1)->order('id DESC')->paginate(10); // List pagination
$cate->where('id', 1)->delete(); // Delete
```