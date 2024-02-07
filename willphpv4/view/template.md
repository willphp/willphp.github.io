## Template Syntax

#### Constant Output

| Constant         | Description |
|------------------|-------------|
| \_\_VERSION\_\_  | Framework version |
| \_\_POWERED\_\_  | Framework name + version number |
| \_\_WEB\_\_      | Relative URL |
| \_\_URL\_\_      | Current URL |
| \_\_HISTORY\_\_  | Source URL |
| \_\_ROOT\_\_     | Root path |
| \_\_STATIC\_\_   | Resource directory |
| \_\_UPLOAD\_\_   | Upload directory |
| \_\_THEME\_\_    | Current theme name |

The above constants can be directly output in the template, for example:

```
Powered By __POWERED__ Theme:__THEME__
```

#### Including Files

```
{include file='public/header.html'}
```

#### Template Layout

```
{layout name='layout/layout.html'}
Main Content
```

#### Layout File

```
{include file='public/header.html'}
{__CONTENT__}
{include file='public/footer.html'}
```

#### Variable Assignment

```
{php $hello='hello WillPHP'}
{php $time=time()}
{php $list=db('user')->where('status',1)->select()}
```

#### Variable Output

```
{$hello}
{$arr.id} 
{$arr[0]}
{$arr.0.a.b}
{$arr[$cid]}
{$arr[$vo['cid']]}
{$vo.name|default='default value'} // Variable setting default value
{session('user.nickname') ?: 'Guest'} // Variable or expression setting default
```

#### Variable Operations

```
{$id|intval}
{$vo.title|substr=0,10}
{$list->links()}
```

#### Function Output

```
{:date('Y-m-d',$vo['ctime'])} 
{:url('abc/abc')}
{:widget('test')->get()}
```

#### Conditional Statements

```
{php $id=2}
{if $id==2 ? '2' : '0'} // Ternary operation
{if $id==1 or $id==2:} id=1|2 {/if}
{if $id==1:} id=1 {else:} id<>1 {/if}
{if $id==1:} id=1 {elseif $id==2:} id=2 {else:} id<>1|2 {/if}
{php $none=''}
{empty $none:} none {/empty}
```

#### Loop Statements

```
{php $arr=[['id'=>1,'name'=>'a'],['id'=>2,'name'=>'b']]}
{foreach $arr as $v} 
	{$v.id}.{$v.name} |
{/foreach}
{foreach $arr[0] as $k=>$v}
	{$k}.{$v} | 
{/foreach}
```

#### Output Raw Content

```
{literal}
    {$hello}
{/literal}
__#POWERED#__ // Output: __POWERED__
{#$hello#} // Output: {$hello}
```

## Custom Tags

All tag replacement settings are in the `config/template.php` configuration file, and rules can be freely set for replacement.

#### Modify Tags, e.g.,

```
'regex_literal' => '/{lite}(.*?){\/lite}/s',
```

Effect after modification:

```
{php $hi='hello'}
Failed: {literal}{$hi}{/literal}
Success: {lite}{$hi}{/lite}
```

#### Add Tags, e.g.,

```
// Add {haha:$variable} in the regex_replace configuration
'/{\s*haha:\$var\s*}/i' => '<?php echo $\\1.' haha'?>',
```

Effect after addition:

```
{php $hi='hello'}
{haha:$hi} // Displays hello haha
```

>Note: In regex_replace, key var { } will be automatically replaced with the corresponding configuration

## Development Examples

#### Data Pagination

```
Get data:
{php $list=db('site')->where('id','>',0)->order('id DESC')->paginate(2)}
Data is empty:
{empty $list->toArray():}none{/empty}
Loop output:
{foreach $list as $vo}
    {$vo.id}.{$vo.cname}
{/foreach}
Pagination HTML:
{$list->links()}
Total records:
{$list->getAttr('count')} 
Current page number:
{$list->getAttr('page')} 
Start count:
{$list->getAttr('offset')}
Number of records per page:
{$list->getAttr('page_size')}
Total number of pages:
{$list->getAttr('page_count')}
```

#### Get Configuration

```
{:site('site_title')} equals {:config('site.site_title')}
```

#### Generate URL

```
{:url('blog/about')}
```

#### Format Time

```
{php $vo['ctime']=time()}
{:date('Y-m-d',$vo['ctime'])} 
```

#### String Substring

```
{php $title='hello world'}
{$title|str_substr=0,5}
```

#### Get IP Address

```
{:get_ip()}
```

#### Captcha Image

```
<img src="{:url('api/captcha')}" style="cursor:pointer;" onclick="this.src='{:url('api/captcha')}?'+Math.random();"/>
```