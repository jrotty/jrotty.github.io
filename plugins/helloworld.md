## 一、基本结构

### 1.文件结构
```
HelloWorld  插件文件夹
     |
     |——Plugin.php   插件核心文件
```
插件文件夹命名与插件名、插件类名保持一致，插件主体代码编写在 Plugin.php 中。其中，类名要加上后缀 _Plugin，如下：
```
class HelloWorld_Plugin implements Typecho_Plugin_Interface
{
```
关于命名规范、编码风格等，可以查看http://docs.typecho.org/phpcoding

### 2.注释
```
/**
 * Hello World
 * 
 * @package HelloWorld 
 * @author qining
 * @version 1.0.0
 * @link http://typecho.org
 */
```
 - Hello World: 插件描述
 - @package: 插件名称
 - @author: 插件作者
 - @version: 插件版本
 - @link: 插件作者链接

### 3.插件主体
```php
/* 激活插件方法 */
public static function activate(){}
 
/* 禁用插件方法 */
public static function deactivate(){}
 
/* 插件配置方法 */
public static function config(Typecho_Widget_Helper_Form $form){}
 
/* 个人用户的配置方法 */
public static function personalConfig(Typecho_Widget_Helper_Form $form){}
 
/* 插件实现方法 */
public static function render(){}
```
 - activate: 插件的激活接口，主要填写一些插件的初始化程序。
 - deactivate: 这个是插件的禁用接口，主要就是插件在禁用时对一些资源的释放。
 - config: 插件的配置面板，用于制作插件的标准配置菜单。
 - personalConfig: 个人用户的配置面板，基本用不到。
 - render: 自己定义的方法，用来实现插件要完成的功能。

## 二、实现过程
### 1.插件分析
插件功能，是为了实现用户登录后，在后台菜单导航栏输出欢迎话语，所以我们要做的，就是找找后台菜单文件，是否有提供到此类功能的插件接口。一般来说，用哪个接口来实现功能，是要看我们要写的插件，用到哪一方面的功能，或者实现哪些效果来判断，再到对应的文件去寻找。很幸运，我们在[/admin/menu.php][1] 中找到了以下接口，大概在第7行左右：
```php
<?php Typecho_Plugin::factory('admin/menu.php')->navBar(); ?>
```
这就是我们要在激活插件里要写入的接口代码。插件接口，常以下面的两种方式出现：
```php
Typecho_Plugin::factory()->function();
$this->pluginHandle()->function();
```
我们找好接口代码后，下面便开始编写我们的插件代码。

### 2.编写代码
平常编写代码的顺序，基本按照默认办法出现的顺序来编写。所以，我们先开始写激活接口代码：
```php
public static function activate()
{
    Typecho_Plugin::factory('admin/menu.php')->navBar = array('HelloWorld_Plugin', 'render');
}
```
其中 = 号前面的那段代码，便是我们上面所找到的接口代码，只需要把它复制进来即可，后面是我们插件要实现的方法，这段代码会在接口处运行。
```php
array('HelloWorld_Plugin', 'render');
// 赋值以数组形式出现
// HelloWorld_Plugin 插件的类名，一般是插件名加上“_Plugin”，其中类名还可以用__CLASS__，不过经常是直接把插件类名写上
// render 插件实现的方法名，后面插件实现方法的命名要与此一致
```
该插件注销时没有什么资源需要释放，所以禁用方法就不需要编写了。接下来是编写配置方法。登录后台后，欢迎话语因人而已，所以我们需要给个配置表单给用户，由他们自己定制。因此，我们在配置方法里，可以写上一个配置欢迎话语的表单：
```php
public static function config(Typecho_Widget_Helper_Form $form)
{
    /** 配置欢迎话语 */
    $name = new Typecho_Widget_Helper_Form_Element_Text('word', NULL, 'Hello World', _t('说点什么'));
    $form->addInput($name);
}
```
表单助手类参数说明：

**1,** word：配置项命名
**2,** NULL：选项，因为这是个文本输入框，所以是NULL
**3,** Hello World：默认值
**4,** _t('说点什么')：表单的 label 标题，它后面还有一个参数是描述

要想了解更多使用，可以查阅[/var/Typecho/Widget/Helper/Form/Element.php][2]

```php
$form->addInput($name);
```

这句则是把定义的变量写入到配置项中，以便后面使用。接下来，便是我们插件实现的方法。

因上面激活接口那，我们把插件实现的方法名取为 render，所以我们要在插件里，自定义一个名为 render 的函数：
```php
public static function render()
{
    // 逻辑代码
}
```
接下来，我们要显示已经自定义好的欢迎话语，所以逻辑代码里，我们可以这么写：
```php
echo '<span class="message success">' . Typecho_Widget::widget('Widget_Options')->plugin('HelloWorld')->word . '</span>';
```
其中，下面这句是调用插件配置项的：
```php
Typecho_Widget::widget('Widget_Options')->plugin('HelloWorld')->word
```
方式是：Options + 插件名(不带_Plugin) + 配置项名

当然，你也可以通过 Helper 助手来获取。
```
Helper::options()->plugin('HelloWorld')->word
```
更多 Helper 助手用法，请查阅[/var/Helper.php][3]

至此，我们的 HelloWorld 插件已完成，感谢！欢迎诸君多分享插件！


  [1]: https://github.com/typecho/typecho/blob/master/admin/menu.php
  [2]: https://github.com/typecho/typecho/blob/master/var/Typecho/Widget/Helper/Form/Element.php
  [3]: https://github.com/typecho/typecho/blob/master/var/Helper.php