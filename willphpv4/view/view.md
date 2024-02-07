## View Configuration

#### Template Paths

You can configure the view paths for your application in `config/app.php`, as shown below:

```php
'view_path' => ['index' => 'view'], // Set the index template path to the root directory view
'theme_on' => ['index'], // Set index as multi-theme
```

#### Theme Switching

The default theme for the application is set in `config/site.php`:

```php
'theme' => 'default',
```

You can switch themes by using GET parameters, and the selected theme will be stored in a cookie, for example:

```php
?t=blue // Switch to the blue theme and store it in a cookie
```

>If the selected theme does not exist, the `default` theme will be used.

## Value Rendering

You can assign and render view variables in your controller, as demonstrated below:

```php
public function index(): object
{
    //return view_with(['id'=>1]);     
    //return view();
    //return view('index/test1', ['id' => 2,'msg' => 'php']);
    //return view()->cache(30);
    return view()->with('id', 3);
}
```