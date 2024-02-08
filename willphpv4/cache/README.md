#### Widget Definition

Define widget classes in `app/application_name/widget`, for example:

```php
declare(strict_types=1);
namespace app\index\widget;
use willphp\core\Widget;
class Test extends Widget
{
    protected string $tagName = 'test'; // Widget tag name (used for automatic update)
    protected int $expire = 0; // Cache time (seconds) 0 permanent
    // Set widget data (required)
    public function set($id = '', array $options = [])
    {
        return $id.'-'.date('Y-m-d H:i:s');
    }
}
```

#### Using Widgets

You can use widgets in controllers, for example:

```php
declare(strict_types=1);
namespace app\index\controller;
class Index
{
    public function index(int $id = 0)
    {  
        $time = widget('test')->get($id);
        echo $time;
        // widget('index.test') // Get across applications
    }
}
```

#### Template Invocation

```php
{:widget('test')->get()
```

#### Update Widget

```php
widget('test')->update();
```

#### Clear Cache

```php
cache(null);
```