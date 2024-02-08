## Database Configuration

You can configure database connections in the global `config/database.php` file or the local `.env` file, for example:

```php
'default' => [
    'db_type' => 'mysql', // Database driver type
    'db_host' => 'localhost', // Database server
    'db_port' => '3306', // Server port
    'db_user' => 'root', // Database username
    'db_pwd' => '', // Database password
    'db_name' => 'myapp01db', // Database name
    'db_prefix' => 'wp_', // Database table prefix
    'db_charset' => 'utf8', // Default character encoding
    //'pdo_params' => ...
],
'read' => [
    'db_host' => '127.0.0.1', // Server
    'db_name' => 'myapp02db', // Database name
],
```

## Getting Connections

By default, use the `default` configuration, and you can switch or modify configurations, for example:

```php
db('blog'); // Single table (Note: Table name does not include prefix)
db()->table('blog,cates'); // Multiple tables
db('blog', 'read'); // Use read configuration
db('blog', ['db_name'=>'db03']); // Connect to database db03
```

To switch or modify in the model, you can set class properties, for example:

```php
protected string $dbConfig = 'read';
```

> Note: New configurations that do not exist will use the `default` configuration.

#### Parameter Binding

Execute SQL statements with parameter binding, for example:

```php
db()->execute('update wp_test set sn=:sn where id=:id',['sn'=>'a','id'=>1]);
db()->query('select * from wp_test where id=? AND sn=?',[1, 'a']);
```

## Data Query

#### Set Alias

```php
db('blog')->alias('a'); 
db()->table('blog a');
db()->table('blog a,cate b');
```

#### Set Fields

```php
db('blog')->field('id,title'); // Support array  
db('blog')->field('content', true); // Exclude fields 4.3+   
db()->table('blog a,cate b')->field('a.*,b.cname');
```

#### Set Conditions

```php
db('blog')->where(['id'=>1,'status'=>1]); //'id=1 AND status=1'
db('blog')->where('id', '=', 1, 'or')->where('id, 2); //id=1 OR id=2 
```

#### Set Sorting

```php
db('blog')->order('id ASC,cid DESC'); // Support array ['id'=>'asc','cid'=>'desc']
db('blog')->order('id','desc');
```

#### Set Limit

```php
db('blog')->limit(10);
db('blog')->limit(0,10); // Same as '0,10'
```

#### Single Row Query

```php
$blog = db('blog');
$blog->where('id', 1)->find();
$blog->find(1); // Same as above
$blog->getById(1); // Same as above
$blog->where('id', 1)->value('title'); // Return the value of title
```

> Querying a single row will automatically add `limit(1)`

#### Multiple Row Query

```php
db('blog')->order('id DESC')->limit(10)->select(); // Two-dimensional array
db('cate')->column('cname', 'id'); //[id=>cname] One-dimensional array
db('cate')->column('id,cname'); // Return two-dimensional array
db('cate')->column('*', 'id'); // Return two-dimensional array
```

#### Multiple Row Pagination

```php
$list = db('blog')->order('id DESC')->paginate(5); // 5 items per page
foreach ($list as $vo) {
    echo '<p>'.$vo['id'].'</p>';
}
echo $list->links(); // Pagination HTML 
```

#### Paginated Query

```php
$p = 1; // Current page number, starting from 1
if (isset($_GET['p'])) {
    $p = max(1, intval($_GET['p']));
}
db('blog')->page($p, 10)->select(); // 10 items per page
```

#### Aggregate Query

```php
db('blog')->where('cid', 1)->count(); // Total number of records   
db('blog')->sum('hits'); // Total
db('blog')->min('hits'); // Minimum
db('blog')->max('hits'); // Maximum
db('blog')->avg('hits'); // Average
```

#### Associated Query

```php
db()->table('blog a')->join('cate b', 'a.cid=b.id', 'left')
->field('a.*,b.cname')->select();
// SQL:
// SELECT `a`.*,`b`.`cname` FROM `wp_blog` `a` 
// LEFT JOIN `wp_cates` `b` ON `a`.`cid`=`b`.`id`
```

#### Object Preprocessing

```php
$db = db('blog')->where('status', 1);
$db->where('id', 1)->find(); // status=1 AND id=1
$db->where('id', 2)->find(); // status=1 AND id=2
```

#### Get SQL

```php
$sql0 = db('blog')->getSql()->count();
dump($sql0);
$db = db('blog')->getSql();
$sql1 = $db->where('id', 1)->find();
$sql2 = $db->order('id DESC')->select();
dump($sql1, $sql2);
```

#### Other Preprocessing

```php
union       // Union query
group       // Set group query
having      // Set having query
using       // USING (multi-table delete)
extra       // Set query extra parameters
duplicate   // Set DUPLICATE
lock        // Query lock
distinct    // Distinct query
force       // Specify forced index
comment     // Query comments
```

#### Other Post-processing

```php
$blog = db('blog');
$blog->table('site')->getTable(); // Get table name
$blog->getPk(); // Get table primary key field
$blog->getPrefix(); // Get table prefix
$blog->getFieldList(); // Get table field list
```

## Data Deletion

```php
db('blog')->where('id', 1)->delete(); 
db('blog')->delete(1); 
db('blog')->delete([2,3]);
```

> There must be a deletion condition, and the return value is the number of affected rows.

## Data Insertion

```php
$data = [
    ['cname' => 'Category 1', 'sort' => '1'],
    ['cname' => 'Category 2', 'sort' => '2'],
];
$cate = db('cate');
$cate->insert($data[0]); // Return the number of affected rows
$cate->insertGetId($data[0]);// Return the new id
$cate->replace($data[

0]); // Replace
$cate->data($data[0])->insert(); // Set data first
$cate->insertAll($data); // Batch insertion
$cate->field('cname')->insertAll($data); // Field restriction
```

## Data Modification

```php
$data = ['cname' => 'Category 1', 'sort' => '1'];
$cate = db('cate');
$cate->where('id', 1)->update($data);
$cate->where('id', 1)->setField('cname', '123'); // Set field value
$cate->where('id', 1)->setInc('sort', 2); // Auto-increment (setDec for decrement)
$cate->where('id', 1)->inc('sort')->update(); // Auto-increment (dec for decrement)
$cate->where('id', 1)->data('sort', ['inc', 1])->update();
```

> There must be an update condition, and the return value is the number of affected rows.

## Query Conditions

Simple queries:

```php
where('id', 1)->where('cid=1')  //id=1 AND cid=2
where('id', '>', 0)             // Supports =,<>,>,<,>=,<=
where('id', 'in', '1,2')        // in, not in supports arrays
where('name', 'like', '%ad%')   // like, not like
where('id', 'between', '1,3')   // between, not between
where('id', 'exp', 'IN (2,3)')  // Expression query, SQL syntax 
```

Connection method:

```php
where('id', 1)->where('status', 1); 
where('id', '=', 1, 'or')->where('id',2)->where('status',1); 
```

Array query:

```php
$map = [];
$map[] = ['id', '=', 1, 'or']; 
$map[] = ['username', 'like', '%admin%'];
$map['status'] = 1;    
where($map)
where(['id'=>1,'status'=>1])
```

## Transaction Processing

The database must support transactions in order to use them:

```php
db()->startTrans(); // Start transaction
$r1 = db('cate')->add(['cname'=>'willphp']);
$r2 = db('blog')->where('id', 1)->setField('hits', 1);
if ($r1 && $r2) {           
    db()->commit(); // Commit transaction
    echo 'Submitted successfully';
} else {
    db()->rollback(); // Rollback transaction
    echo 'Submission failed';
}
```

#### Fast Transaction

```php
db()->trans(function() {
    db('cate')->add(['cname'=>'willphp']);
    db('blog')->where('id', 1)->setField('hits', 1);
}); 
```