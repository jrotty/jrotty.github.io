### 说明
官方默认模板的title(html中的`<title>`)如下：
  
```php
<?php $this->archiveTitle(array(
            'category'  =>  _t('分类 %s 下的文章'),
            'search'    =>  _t('包含关键字 %s 的文章'),
            'tag'       =>  _t('标签 %s 下的文章'),
            'author'    =>  _t('%s 发布的文章')
        ), '', ' - '); ?><?php $this->options->title(); ?>
```
输出结果：...页面标题... - 站点名称

让我们来分解一下其中的语句，后一句大家都很明白，显示站点名称嘛，那前一句呢？其实前一句的标题包含三个参数：
```php
<?php $this->archiveTitle($split, $before, $end); ?>
```
| 参数名称 | 默认值 | 简介 |
|:--|:--|:--|
| $split | &raquo; | 多级菜单间的分隔符，如：2009 » 12 |
| $before | &raquo; | title 前显示的字符 |
| $end |  | title 后显示的字符 |

### 建议
其实官方默认这样就已经很好了，但是如果有seo优化需要建议在后面再加上页码信息，如：

```php
<?php $this->archiveTitle(array(
            'category'  =>  _t('分类 %s 下的文章'),
            'search'    =>  _t('包含关键字 %s 的文章'),
            'tag'       =>  _t('标签 %s 下的文章'),
            'author'    =>  _t('%s 发布的文章')
        ), '', ' - '); ?><?php $this->options->title(); ?><?php if($this->_currentPage>1) echo ' - 第 '.$this->_currentPage.' 页 '; ?>
```
OK，目标达到，剩下就是慢慢等搜索引擎更新标题吧。
