## index.php

### 模板信息

我们先从主文件说起，打开这个文件，首先看到的是注释：

```html
/**
 * 这是typecho系统的一套默认皮肤。你可以在<a href="http://typecho.org">typecho的官方网站</a>获得更多关于此皮肤的信息
 * 
 * @package Typecho Default Theme 
 * @author typecho
 * @version 1.0.0
 * @link http://typecho.org
 */
```

这是模板信息存放的地方，它将在后台的模板选择页显示。前两行是简短的介绍，每个“\*”表示一个段落。

- **@package** 表示模板名
- **@author**表示作者名
- **@version**是模板的版本号
- **@link**是作者的网站连接

紧挨着注释下方的`$this->need('header.php')`，在结尾处也会看到`$this->need('sidebar.php')`和`$this->need('footer.php')`。这些语句用来调用模板的其它模块。header故名思议是页首，sidebar是侧栏，footer是页脚。(与php的include功能差不多，need是typecho程序内置的方法，内部还会存在一些判断什么的，建议在做主题是使用need方法而不是include)

### 显示文章列表

```html
<?php if ($this->have()): ?>
<?php while($this->next()): ?>
<a href="<?php $this->permalink() ?>"><?php $this->title() ?></a>
作者:<a href="<?php $this->author->permalink(); ?>"><?php $this->author(); ?></a>
时间: <?php $this->date(); ?>
分类: <?php $this->category(','); ?>
<a href="<?php $this->permalink() ?>#comments"><?php $this->commentsNum('评论', '1 条评论', '%d 条评论'); ?>
标签：<?php $this->tags(','); ?>
<?php $this->content('- 阅读剩余部分 -'); ?>
<?php endwhile; ?>
<?php else: ?>暂无文章<?php endif; ?>
```

进入文章循环，输出文章，一句一句介绍

| 代码 | 解释 |
|:--|:--|
|`<?php if ($this->have()): ?>`| 判断是否有文章，没有的话输出提示 |
| `<?php while($this->next()): ?>` | 开始循环输出文章，与`<?php endwhile; ?>`对应 |
| `<?php $this->permalink() ?>` | 文章所在的连接 |
| `<?php $this->title() ?>` | 文章标题 |
| `<?php $this->author(); ?>` | 文章作者 |
| `<?php $this->author->permalink(); ?>` | 文章作者地址 |
| `<?php $this->date(); ?>` | 文章的发布日期，日期格式可在typecho后台**设置->阅读**中设置|
| `<?php $this->category(','); ?>` | 文章所在分类 |
| `<?php $this->tags(','); ?>` | 文章标签 |
| `<?php $this->commentsNum('评论', '1 条评论', '%d 条评论'); ?>` | 文章评论数及连接 |
| `<?php $this->content('- 阅读剩余部分 -'); ?>` | 文章内容，其中的“- 阅读剩余部分 -”是显示摘要时隐藏部分的邀请链接，也可使用`<?php $this->excerpt(140, '...'); ?>`来进行自动截取文字内容，“140”是截取字符数量 |

### 文章分页

```php
<?php $this->pageNav('«前一页', '后一页»'); ?>
```

也可以这样分开写

```php
<?php $this->pageLink('下一页','next'); ?>
<?php $this->pageLink('上一页'); ?>
```

### 其他说明

archive.php代码同index.php，区别就是index.php是显示首页的，而archive.php是显示某分类下的文章列表、搜索结果的。如果模板文件中不存在archive.php，程序就会自动用index.php代替archive.php。


## header.php

### 编码

打开这个文件，见到的第一个php代码就是：
```php
<meta charset="<?php $this->options->charset(); ?>">
```
调用默认的编码，现在最经常用都是utf-8吧。所以我通常是直接写成utf-8，省去php处理时间。

#### 页面标题

```php
<?php $this->archiveTitle(array(
            'category'  =>  _t('分类 %s 下的文章'),
            'search'    =>  _t('包含关键字 %s 的文章'),
            'tag'       =>  _t('标签 %s 下的文章'),
            'author'    =>  _t('%s 发布的文章')
        ), '', ' - '); ?><?php $this->options->title(); ?>
```
`<?php $this->archiveTitle(); ?>`是当前页面的标题，`<?php $this->options->title(); ?>`是网站的标题。

### 导入样式

```php
<link rel="stylesheet" href="<?php $this->options->themeUrl('style.css'); ?>">
```
其中style.css是样式表文件相对模板目录的路径和文件名。
### 其它HTML头部信息
```php
<?php $this->header(); ?>
```
这是typecho的自有函数，会输出HTML头部信息；同时这个也是头部插件接口，有了它插件可以向网站头部插入css或者js代码。

### 网站名称与logo
```html
<?php if ($this->options->logoUrl): ?>
<a id="logo" href="<?php $this->options->siteUrl(); ?>">
<img src="<?php $this->options->logoUrl() ?>" alt="<?php $this->options->title() ?>" />
</a>
<?php else: ?>
<a id="logo" href="<?php $this->options->siteUrl(); ?>"><?php $this->options->title() ?></a>
<p class="description"><?php $this->options->description() ?></p>
<?php endif; ?>
```
第一句的if判断是判断模板是否通过模板设置设置了logo的地址，如果设置了就显示logo图片，否则就显示博客标题。
`<?php $this->options->siteUrl(); ?>`是网站地址
`<?php $this->options->title() ?>`是网站名字
`<?php $this->options->description() ?>`是网站描述。
logo部分的讲解将会在**functions.php**章节中详细讲解。

### 站内搜索

```html
<form id="search" method="post" action="<?php $this->options->siteUrl(); ?>" role="search">
<input type="text" id="s" name="s" class="text" placeholder="<?php _e('输入关键字搜索'); ?>" />
<button type="submit" class="submit"><?php _e('搜索'); ?></button>
</form>
```
当你的文章很多很多，这个搜索就必不可少。

### 页面导航
```html
<nav id="nav-menu" class="clearfix" role="navigation">
<a<?php if($this->is('index')): ?> class="current"<?php endif; ?> href="<?php $this->options->siteUrl(); ?>"><?php _e('首页'); ?></a>
<?php $this->widget('Widget_Contents_Page_List')->to($pages); ?>
<?php while($pages->next()): ?>
<a<?php if($this->is('page', $pages->slug)): ?> class="current"<?php endif; ?> href="<?php $pages->permalink(); ?>" title="<?php $pages->title(); ?>"><?php $pages->title(); ?></a>
<?php endwhile; ?>
</nav>
```
其中`<?php $this->options->siteUrl(); ?>`是网站地址，然后下面的while循环是循环输出独立页面的，其中`<?php $pages->permalink(); ?>`是独立页面的超链接，`<?php $pages->title(); ?>`是独立页面的标题。



## sidebar.php

### 最新文章列表

```html
<ul class="widget-list">
<?php $this->widget('Widget_Contents_Post_Recent')
            ->parse('<li><a href="{permalink}">{title}</a></li>'); ?>
</ul>
```
获取最新的10篇文章标题，得到的html是
```html
<ul class="widget-list">
<li><a href="http://example.com/2008/12/31/sample-post-one">文章1的标题</a></li>
<li><a href="http://example.com/2008/12/31/sample-post-two">文章2的标题</a></li>
    <!-- 省略n个重复 -->
<li><a href="http://example.com/2008/12/31/sample-post-ten">文章10的标题</a></li>
</ul>
```
具体显示数量可在typecho后台**设置->阅读**中设置。

### 最新回复列表
```html
<ul class="widget-list">
<?php $this->widget('Widget_Comments_Recent')->to($comments); ?>
<?php while($comments->next()): ?>
<li><a href="<?php $comments->permalink(); ?>"><?php $comments->author(false); ?></a>: <?php $comments->excerpt(35, '...'); ?></li>
<?php endwhile; ?>
</ul>
```
获取最新的10个回复，得到的html是
```html
<ul class="widget-list">
    <li>回复人名字: <a href="http://example.com/2008/12/31/sample-post#comments-12">回复的内容...</a></li>
    <li>回复人名字: <a href="http://example.com/2008/12/31/sample-post#comments-11">回复的内容...</a></li>
    <!-- 省略n个重复 -->
</ul>
```
其中`<?php $comments->excerpt(35, '...'); ?>`，“35”代表要回复内容截取的字的个数，“…”代表省略的意思，你可以自行修改。具体显示数量可在typecho后台**设置->评论**中设置。

### 文章分类列表
```php
<?php $this->widget('Widget_Metas_Category_List')->listCategories('wrapClass=widget-list'); ?>
```
效果如下
```html
<ul class="widget-list">
<li class="category-level-0 category-parent"><a href="分类1链接">分类1</a></li>
<li class="category-level-0 category-parent"><a href="分类2链接">分类2</a></li>
 <!-- 省略n个重复 -->
</ul>
```
如果有个分类3，分类4是上述分类2的子分类，那么效果如下
```html
<ul class="widget-list">
<li class="category-level-0 category-parent"><a href="分类1链接">分类1</a></li>
<li class="category-level-0 category-parent"><a href="分类2链接">分类2</a>
  <ul class="widget-list">
    <li class="category-level-1 category-child category-level-odd"><a href="分类3链接">分类3</a></li> 
    <li class="category-level-1 category-child category-level-odd"><a href="分类4链接">分类4</a></li>
  </ul>
</li>
 <!-- 省略n个重复 -->
</ul>
```

### 按月归档

```html
<ul class="widget-list">
            <?php $this->widget('Widget_Contents_Post_Date', 'type=month&format=F Y')
            ->parse('<li><a href="{permalink}">{date}</a></li>'); ?>
</ul>
```
输出：
```html
<ul class="widget-list">
<li><a href="http://example.com/2018/11">November 2018</a></li>
<li><a href="http://example.com/2018/10">October 2018</a></li>
</ul>
```

### 登陆登出

```html
<?php if($this->user->hasLogin()): ?>
<li class="last"><a href="<?php $this->options->adminUrl(); ?>"><?php _e('进入后台'); ?> (<?php $this->user->screenName(); ?>)</a></li>
<li><a href="<?php $this->options->logoutUrl(); ?>"><?php _e('退出'); ?></a></li>
<?php else: ?>
<li class="last"><a href="<?php $this->options->adminUrl('login.php'); ?>"><?php _e('登录'); ?></a></li>
<?php endif; ?>
```
这些是可有可无的，只是为了方便登录登出。`<?php $this->options->adminUrl(); ?>`是后台地址，`<?php $this->user->screenName(); ?>`用户昵称，`<?php $this->options->logoutUrl(); ?>`登出链接，`<?php $this->options->adminUrl('login.php'); ?>`登陆链接。

### RSS地址

```html
<a href="<?php $this->options->feedUrl(); ?>"><?php _e('文章 RSS'); ?></a> <!-- 文章的RSS地址连接 -->
<a href="<?php $this->options->commentsFeedUrl(); ?>"><?php _e('评论 RSS'); ?></a><!-- 评论的RSS地址连接 -->
```


## footer.php

页脚文件，推荐大家把一些较大的js放在这个文件中最后载入，不会影响阅读。看看我们的footer要讲解些什么？

### 版权声明等
```html
&copy; <?php echo date('Y'); ?> <a href="<?php $this->options->siteUrl(); ?>"><?php $this->options->title(); ?></a>.
    <?php _e('由 <a href="http://www.typecho.org">Typecho</a> 强力驱动'); ?>
```
* `<?php echo date('Y'); ?>`是当前年份
* `<?php $this->options->siteUrl(); ?>`是网站地址
* `<?php $this->options->title(); ?>`是网站标题。

### 插件接口

```php
<?php $this->footer(); ?>
```
用于插件向页脚插入css，js文件等。


## post.php

post页和index是差不多的，下面解释下post.php里面存在的php代码。

### 代码与说明

|代码|解释|
|:--|:--|
|`<?php $this->permalink() ?>`|文章地址|
|`<?php $this->title() ?>`|文章标题|
|`<?php $this->author->permalink(); ?>`|文章作者主页链接|
|`<?php $this->author(); ?>`|文章作者昵称|
|`<?php $this->date(); ?>`|文章发布时间|
|`<?php $this->category(','); ?>`|文章分类，多个分类中间用逗号隔开|
|`<?php $this->content(); ?>`|文章内容|
|`<?php $this->tags(', ', true, 'none'); ?>`|文章标签，多个标签间用逗号隔开，标签以带超链接的形式显示，如果不存在标签则显示none|
|`<?php $this->need('comments.php'); ?>`|调用评论页|
|`<?php $this->thePrev('%s','没有了'); ?>`|带有超链接的上一篇文章的标题|
|`<?php $this->theNext('%s','没有了'); ?>`|带有超链接的下一篇文章的标题|

### 其他说明

page.php代码同post.php，区别就是post是用来显示文章的，而page.php是用来显示独立页面的。


## comments.php

### 评论列表
```php
<?php $this->comments()->to($comments); ?>
<?php if ($comments->have()): ?>
<h3><?php $this->commentsNum(_t('暂无评论'), _t('仅有一条评论'), _t('已有 %d 条评论')); ?></h3>
<?php $comments->listComments(); ?>
<?php $comments->pageNav('&laquo; 前一页', '后一页 &raquo;'); ?>
<?php endif; ?>
```

判断文章是否存在评论，如果存在就输出评论；其中`<?php $comments->listComments(); ?>`是评论列表，`<?php $comments->pageNav('&laquo; 前一页', '后一页 &raquo;'); ?>`是评论翻页按钮。

### 评论输入表单

```html
<!-- 判断设置是否允许对当前文章进行评论 -->
<?php if($this->allow('comment')): ?>
    <div id="<?php $this->respondId(); ?>" class="respond">
        <div class="cancel-comment-reply">
        <?php $comments->cancelReply(); ?>
        </div>
    
    	<h3 id="response"><?php _e('添加新评论'); ?></h3>
<!-- 输入表单开始 -->
    	<form method="post" action="<?php $this->commentUrl() ?>" id="comment-form" role="form">
<!-- 如果当前用户已经登录 -->
            <?php if($this->user->hasLogin()): ?>
<!-- 显示当前登录用户的用户名以及登出连接 -->
    		<p><?php _e('登录身份: '); ?><a href="<?php $this->options->profileUrl(); ?>"><?php $this->user->screenName(); ?></a>. <a href="<?php $this->options->logoutUrl(); ?>" title="Logout"><?php _e('退出'); ?> &raquo;</a></p>
<!-- 若当前用户未登录 -->
            <?php else: ?>
<!-- 要求输入名字、邮箱、网址 -->
    		<p>
                <label for="author" class="required"><?php _e('称呼'); ?></label>
    			<input type="text" name="author" id="author" class="text" value="<?php $this->remember('author'); ?>" required />
    		</p>
    		<p>
                <label for="mail"<?php if ($this->options->commentsRequireMail): ?> class="required"<?php endif; ?>><?php _e('Email'); ?></label>
    			<input type="email" name="mail" id="mail" class="text" value="<?php $this->remember('mail'); ?>"<?php if ($this->options->commentsRequireMail): ?> required<?php endif; ?> />
    		</p>
    		<p>
                <label for="url"<?php if ($this->options->commentsRequireURL): ?> class="required"<?php endif; ?>><?php _e('网站'); ?></label>
    			<input type="url" name="url" id="url" class="text" placeholder="<?php _e('http://'); ?>" value="<?php $this->remember('url'); ?>"<?php if ($this->options->commentsRequireURL): ?> required<?php endif; ?> />
    		</p>
            <?php endif; ?>
<!-- 输入要回复的内容 -->
    		<p>
                <label for="textarea" class="required"><?php _e('内容'); ?></label>
                <textarea rows="8" cols="50" name="text" id="textarea" class="textarea" required ><?php $this->remember('text'); ?></textarea>
            </p>
    		<p>
                <button type="submit" class="submit"><?php _e('提交评论'); ?></button>
            </p>
    	</form>
    </div>
<!-- 若当前文章不允许进行评论 -->
    <?php else: ?>
    <h3><?php _e('评论已关闭'); ?></h3>
    <?php endif; ?>
```
具体请对应上述代码中注释自行理解。


## functions.php

**function  themeConfig($form)** 内的代码是模板设置功能

### logo设置

```php
$logoUrl = new Typecho_Widget_Helper_Form_Element_Text('logoUrl', NULL, NULL, _t('站点 LOGO 地址'), _t('在这里填入一个图片 URL 地址, 以在网站标题前加上一个 LOGO'));
$form->addInput($logoUrl);
```

这行代码就是在模板设置处添加一个logo设置，可以添加一个图片地址作为logo，添加好了通过如下代码即可输出这个图片

```html
<!--判断logo已被设置-->
<?php if ($this->options->logoUrl): ?>
<!--给logo图片加上本站超链接-->
<a id="logo" href="<?php $this->options->siteUrl(); ?>">
<!--显示logo-->
<img src="<?php $this->options->logoUrl() ?>" alt="<?php $this->options->title() ?>" />
</a>
<?php endif; ?>
```

此处对应header.php中的logo显示。

### 显示开关

```php
$sidebarBlock = new Typecho_Widget_Helper_Form_Element_Checkbox('sidebarBlock', 
array('ShowRecentPosts' => _t('显示最新文章'),
'ShowRecentComments' => _t('显示最近回复'),
'ShowCategory' => _t('显示分类'),
'ShowArchive' => _t('显示归档'),
'ShowOther' => _t('显示其它杂项')),
 array('ShowRecentPosts', 'ShowRecentComments', 'ShowCategory', 'ShowArchive', 'ShowOther'), _t('侧边栏显示'));  
    $form->addInput($sidebarBlock->multiMode());
```

是一些开关，这里拿ShowCategory举例，如果勾选他
```php
<?php if (!empty($this->options->sidebarBlock) && in_array('ShowCategory', $this->options->sidebarBlock)): ?>
勾选了就会显示这里的文字
<?php endif; ?>
```

这里对应的是sidebar.php中的最新文章，最新评论，文章分类，归档等显示开关。

### 其他说明

参考以上代码，照葫芦画瓢，可以增加自己需要的模板设置。

## 文件结构说明


| 文件名 | 作用 | 必须 |
|:--|:--|:--|
| style.css | 主题样式文件 | 否 |
| screenshot.png | 主题缩略图,图片后缀支持jpg,png,gif,bmp,jpeg | 否 |
| index.php | 首页以及说明文件 | 是 |
| 404.php | 404页面文件 | 否 |
| archive.php | 通用（分类、搜索、标签、作者）页面文件 | 否 |
| category.php | 分类页面文件 | 否 |
| search.php | 搜索页面文件 | 否 |
| tag.php | 标签页面文件 | 否 |
| author.php | 作者页面文件 | 否 |
| comments.php | 评论页面文件 | 否 |
| footer.php | 底部页面文件 | 否 |
| functions.php | 主题函数文件 | 否 |
| header.php | 头部页面文件 | 否 |
| page.php | 独立页面文件 | 否 |
| post.php | 日志页面文件 | 否 |
| sidebar.php | 侧边栏页面文件 | 否 |

**PS：**
如果archive.php不存在，index.php也会作为通用页面，实现archive.php的工作。
