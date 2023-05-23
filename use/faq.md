### 分类/标签计数不准
解决方法：选择所有的分类/标签，点击【选中项】→【刷新】即可。

### 如何自定义附件上传目录
在 `config.inc.php` 里新增一行下列代码即可，其中 `your_upload_dir` 是你要上传的目录：
```php
define('__TYPECHO_UPLOAD_DIR__', 'your_upload_dir');
```

### 升级完后网站提示 500 错误
原因 1：找不到模板。解决方法：进入后台，重新选择模板。
原因 2：权限问题。解决方法：对 `typecho` 目录重新设定权限 `chmod -Rf 644 *`，如果还不行，就执行 `chmod -Rf 755 *``

### https不生效
当你用 `https` 的时候 `typecho` 会自动识别的，但是因为大家的环境千差万别，会有识别错误的情况，比如你 `https` 都布置好了，但是程序依旧只会加载 `http` 的，所以该常量的作用就是强制程序使用 `https`，解决识别错误的情况。 用法 在`config.inc.php`最后面插入

```php
define('__TYPECHO_SECUER__', true);。

```