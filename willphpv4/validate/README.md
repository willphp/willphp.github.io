## Usage :id=validate

```php
// Rules: 'form_field', 'validation rules[|...]', 'error message[|...]', '[validation condition]'
$rule = [
    ['email', 'email', 'Email is incorrect', AT_MUST]
];
$data = ['email' => 'abc@163'];
// Parameters: 'rule settings', '[data ($_POST)]', '[batch validation (false)]'
$v=validate($rule, $data);
$v->show(); // Calls controller Error::_406($err) when failed
$v->getError(); // Returns errors if validation fails, empty array if successful
$v->isFail(); // Returns true if validation fails
```

#### Validation Conditions

```php
const AT_MUST = 1; // Must (default)
const AT_NOT_NULL = 2; // Not null
const AT_NULL = 3; // Null
const AT_SET = 4; // Field set
const AT_NOT_SET = 5; // Field not set
```

>Validation conditions can be filled with constants or numbers, default is AT_MUST

## Validation Rules :id=rule

#### Built-in Rules

```
unique      Unique validation Format: unique:table name,primary key
required    Field must be present and not empty
captcha     Captcha validation
exists      Must have field
notExists   Must not have field
confirm     Fields must match e.g., confirm:password
regex       Regex validation e.g., regex:/^\d{5,20}$/
url         URL validation (filter_var validation)
email       Email validation (filter_var validation)
ip          IP validation (filter_var validation)
float       Float validation (filter_var validation)
int         Numeric validation (filter_var validation)
```

#### Special Rules

```
Regular expressions, e.g., /^\d{5,20}$/
Closure functions, e.g., fn($i)=>($i+1)
```

#### Built-in Functions

You can use custom functions or PHP built-in functions for validation, e.g.,

```
Using PHP built-in function: is_numeric
```

#### Custom Rules

You can define your own regular expression validation rules in the `config/validate.php` configuration file, e.g.,

```
'username' => '/^\w{5,20}$/', // Username
'password' => '/^\w{6,12}$/', // Password
'string' => '/^\w+$/', // Alphanumeric underscore
'number' => '/^[0-9]*$/', // Positive number
'chs' => '/^[\x7f-\xff]+$/', // Chinese characters
'mobile' => '/^1[3-9]\d{9}$/', // Mobile number
'qq' => '/^[1-9][0-9]{4,12}$/', // QQ number
'idcard' => '/^(\d{15}$|^\d{18}$|^\d{17}(\d|X|x))$/', // ID card number
'bankcard' => '/^[1-9][0-9]{18}$/', // Bank card number
```

## Validation Example :id=demo

```php
$rule = [
    ['name', 'required|unique:user,id', 'Required|User already exists', 2], // Must have value
    ['pwd', '/^\w{5,12}$/', 'Password must be 5~12 characters', 2], // Must have value
    ['mobile', 'mobile', 'Mobile number is incorrect', AT_MUST], // Must
    ['email', 'email', 'Email is incorrect', 4], // Field set
    ['age', fn($val)=>($val>=18 && $val<=60), 'Age must be between 18 and 60'],
];
$data = ['name'=>'', 'pwd'=>'123', 'mobile'=>'x12323332333', 'age'=>12];
//$data['email'] = 'aaa';
$errors = validate($rule, $data, true)->getError();
dump($errors);
```