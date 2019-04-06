# Typecho数据库常用api

### 表创建和删除

在Typecho插件开发过程中，往往需要创建自己的表。上文提到`Typecho_Db`类中的`query`函数，可用于执行所有`sql`语句，因此我们使用`query()`来进行表的创建、修改或者删除。
```sql
$db= Typecho_Db::get();
$prefix = $db->getPrefix();
$db->query('create table '.$prefix.'metas xxxxx');
```
注意，使用`query`方式创建表的时候，需要在表明前手动添加`$prefix`前缀，否则在后面的使用过程中会造成困惑。
还可以使用`table.`来代替`$prefix`，`typecho`会自动识别并替换成指定的前缀。

同理，修改或者删除`Typecho`数据库中表，按照同样的方式调用`query`即可。

### 数据查询

**select，查询表数据**
`select`语句是可以说`Typecho`插件开发中最常用的`sql`调用。
```sql
$db = Typecho_Db::get();
$query= $db->select()->from('table.metas');
$result = $db->fetchAll($query);
```
说明：
`typecho`中，.号具有特定的意义，这里`table.metas`表示这是一个`metas`表。实际上，typecho是自动将`table.`的字符使用`str_replace`替换成了`config.inc.php`中设定的前缀。

举例：
a. `$db->select()->from('table.metas');`将生成`SELECT * FROM typecho_metas WHERE (mid= '2' )`，其中`typecho_`是表前缀；
b. 而`$db->select()->from('metas');`将生成`SELECT * FROM metas WHERE (mid= '2' )`，注意这里没有了表前缀。

**指定表字段查询**
有时为了提高查询性能，需要指定查询表中特定的几个字段，那么可以使用下面的方式：
```sql
$query= $db->select('mid','name')->from('table.metas'); 
echo $query; //SELECT `mid` , `name` FROM typecho_metas
```
如果联合查询中，两个表存在相同的字段名，那么可以使用`table.`来指定表名：
```sql
$query = $db->select('table.contents.cid')->from('table.contents')->join....
```
**指定查询条件**
指定`SQL`查询的`where`语句，是最常用的`api`调用。
```sql
$query= $db->select('mid','name')->from('table.metas')->where('mid = ?', 2); 
echo $query; //SELECT * FROM typecho_metas WHERE (`mid` = '2' )
```
如需要指定多个查询条件，直接多次调用`where`即可，将生成`and`关系的`where`条件
```sql
$db->select('mid','name')->from('table.metas')->where('mid = ?', 2)->where('name like ? ', $name);
```
**使用OR关系的查询条件**
可以使用`orWhere()`函数来指定`SQL`查询的或条件。
```sql
$db->select('mid','name')->from('table.metas')->where('mid = ?', 2)->orWhere('mid = ? ', 3);
//SELECT `mid` , `name` FROM typecho_metas WHERE (`mid` = '2' ) OR (`mid` = '3' )
```
**指定查询范围**
在需要分页的场景下，分页是必需的操作。`offset()`和`limit()`分别用于指定起始位置和结束位置，即指定查询范围。
```sql
$query = $db->select('mid','name')->from('table.metas')->offset(2)->limit(3);
echo $query;//SELECT `mid` , `name` FROM typecho_metas LIMIT 3 OFFSET 2
```

`Typecho`中，还提供了一种简写的方法，见`page()`函数。

```
$query = $db->select('mid','name')->from('table.metas')->page(3,10);
echo $query;//SELECT `mid` , `name` FROM typecho_metas LIMIT 10 OFFSET 20
//表示取第三页，并取10条记录。
```

**对查询结果进行排序**
在`Typecho`中，使用`order()`函数和`Typecho_Db::SORT_DESC`指定查询结果的排序方式。
```sql
$query = $db->select('mid','name')->from('table.metas')->order('mid',Typecho_Db::SORT_DESC);
echo $query;//SELECT `mid` , `name` FROM typecho_metas ORDER BY `mid` DESC
Tips: Typecho_Db::SORT_ASC 表示升序排序，Typecho_Db::SORT_DESC表示降序排序
```
### 联合查询

联合查询是`SQL`的常用语法，在`Typecho`中，同样使用内置函数`join()`方便地进行联合查询。
```sql
$query = $db->select()
    ->from('table.contents')
    ->join('table.comments', 'table.contents.cid = table.comments.cid',Typecho_Db::LEFT_JOIN)
    ->where('table.contents.type = ?', 'post');
echo $query;
//SELECT * FROM typechocontents LEFT JOIN typecho_comments ON typecho_contents.`cid` = typecho_comments.`cid` WHERE (typecho_contents.`type` = 'post' )
```

**1. update，更新表数据**
`Typecho`中，使用`update()`函数来进行更新表操作。但注意，`update`操作，需要借助于`query`执行。
```sql
$update = $db->update('table.metas')->rows(array('name'=>'case_in_cn'))->where('mid=?',6);
echo $update;//UPDATE typecho_metas SET `name` = 'some_name' WHERE (`mid`='6' )
//执行后，返回收影响的行数。
$updateRows= $db->query($update);
```

**2. insert，插入数据**
`Typecho`中，使用`insert()`函数来进行表插入操作。同样，`insert`操作需要借助于`query`函数。
```sql
$insert = $db->insert('table.metas')
    ->rows(array('mid' => '22', 'name' => 'hello world'));
//将构建好的sql执行, 如果你的主键id是自增型的还会返回insert id
$insertId = $db->query($insert);
```

**3. delete，删除数据**
`Typecho`中使用`delete()`函数来删除数据表中的行。`delete`操作用于删除数据表中指定的行，同样需要借助`query`函数执行。
```sql
$delete = $db->delete('table.metas')
    ->where('mid = ?', 2);
//将构建好的sql执行, 会自动返回已经删除的记录数
$deletedRows = $db->query($delete);
```

### 数据库调试

**查看查询语句**
在`Typecho`调试过程中，打印`sql`语句往往是很有帮助的。对于大于**5.2**版本的**php**，直接`echo $query`即可，对于小于**5.2**版本，则需要显式调用`__toString()`函数
```sql
$select = $db->select()->from('table.metas');
//如果版本大于php5.2
echo $select;
//如果小于php5.2
echo $select->__toString();
```

