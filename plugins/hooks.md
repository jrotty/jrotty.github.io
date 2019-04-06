### 默认接口
在Typecho中只要这个类是继承自`Typecho_Widget`基类，它就默认具备了这个插件接口。接口开发者可以使用这个接口无缝地向当前的Class中注入方法

比如我要给`Widget_Archive`类增加一个方法获取当前文章的字数(charactersNum)，只需要在你的插件`activate`方法中声明
```php
Typecho_Plugin::factory('Widget_Archive')->___charactersNum = array('MyPlugin', 'charactersNum');
```
注意，我们在方法名前面加三个下划线表示这是一个内部方法。而实现这个方法也很简单，因为系统会将当前的对象作为参数传递给你
```php
public static function charactersNum($archive)
{
    return mb_strlen($archive->text, 'UTF-8');
}
```
那么这个方法就已经植入到`Widget_Archive`中去了，你在模版中可以直接调用如下代码输出它
```php
<?php $this->charactersNum(); ?>
```

### Widget接口

|接口|参数|描述|
|:--|:--|:--|
|indexHandle|$archive Widget_Archive对象<br>$select Typecho_Db_Query对象|当访问最近文章首页以及分页时被触发|
|error404Handle|$archive Widget_Archive对象<br>$select Typecho_Db_Query对象|当访问404页面时被触发|
|singleHandle|$archive Widget_Archive对象<br>$select Typecho_Db_Query对象|当访问单独页面时被触发(文章，页面，附件)|
|categoryHandle|$archive Widget_Archive对象<br>$select Typecho_Db_Query对象|当访问按分类归档页面时被触发|
|tagHandle|$archive Widget_Archive对象<br>$select Typecho_Db_Query对象|当访问按标签归档页面时被触发|
|authorHandle|$archive Widget_Archive对象<br>$select Typecho_Db_Query对象|当访问按作者归档页面时被触发|
|dateHandle|$archive Widget_Archive对象<br>$select Typecho_Db_Query对象|当访问按日期归档页面时被触发|
|search|$keywords 搜索关键词<br>$archive Widget_Archive对象|这是一个独占接口，当访问搜索页面时被触发，当这个接口被实现后，系统自己的搜索动作将不会继续，你需要在这个接口内自己push搜索的数据到Widget_Archive对象，此接口多用于自己实现站内搜索来替换默认的|
|searchHandle|$archive Widget_Archive对象<br>$select Typecho_Db_Query对象|当访问搜索页面时被触发|
|query|$archive Widget_Archive对象<br>$select Typecho_Db_Query对象|Widget_Archive所有的数据库查询动作最终将由一个query方法来执行，此接口在query方法内，多用于hack某些查询语句|
|select|$archive Widget_Archive对象||
|handleInit|$archive Widget_Archive对象<br>$select Typecho_Db_Query对象||
|handle|type$archive Widget_Archive对象<br>$select Typecho_Db_Query对象||
|pageNav|currentPage<br>$totalpageSize<br>$prev<br>$next<br>$splitPage<br>$splitWord||
|headerOptions|$allows$archive Widget_Archive对象||
|header|$header<br>$archive Widget_Archive对象||
|footer|$archive Widget_Archive对象||
|beforeRender|$archive Widget_Archive对象||
|afterRender|$archive Widget_Archive对象||
|commentFeedItem|feedType<br>$comments||
|feedItem|feedType<br>$archive Widget_Archive对象||

### Widget_Feedback
|接口|参数|描述|
|:--|:--|:--|
|comment|$comment<br>content||
|finishComment|$feedback Widget_Feedback对象||
|trackback|$trackback<br>content||
|finishTrackback|$feedback Widget_Feedback对象||

### Widget_Login
|接口|参数|描述|
|:--|:--|:--|
|loginFail|user<br>name<br>password<br>remember||
|loginSucceed|<br>user<br>name<br>password<br>remember||

### Widget_Logout
|接口|参数|描述|
|:--|:--|:--|
|logout|无||

### Widget_Register
|接口|参数|描述|
|:--|:--|:--|
|register|$dataStruct||
|finishRegister|$register Widget_Register对象||

### Widget_Upload
|接口|参数|描述|
|:--|:--|:--|
|beforeUpload|$result||
|upload|$upload Widget_Upload对象||
|beforeModify|$result||
|modify|$upload Widget_Upload对象||

### Widget_User
|接口|参数|描述|
|:--|:--|:--|
|login|$name<br>$password<br>$temporarily<br>$expire||
|hashValidate|$password<br>$user['password']||
|loginSucceed|$user Widget_User对象<br>$name<br>$password<br>$temporarily<br>$expire||
|loginFail|$user Widget_User对象<br>$name<br>$password<br>$temporarily<br>$expire||
|logout|无||

### Widget_XmlRpc
|接口|参数|描述|
|:--|:--|:--|
|textFilter|$input['text']<br>$xmlRpc Widget_XmlRpc对象||
|upload|$xmlRpc Widget_XmlRpc对象||
|pingback|$pingback<br>$post||
|finishPingback|$xmlRpc Widget_XmlRpc对象||

### Widget_Abstract_Comments

|接口|参数|描述|
|:--|:--|:--|
|content|$text<br>$comments Widget_Abstract_Comments对象|以下句柄同样具有此接口:<br>Widget_Feedback<br>Widget_Comments_Admin<br>Widget_Comments_Archive<br>Widget_Comments_Edit<br>Widget_Comments_Ping<br>Widget_Comments_Recent||
|contentEx|$text<br>$comments Widget_Abstract_Comments对象|以下句柄同样具有此接口:<br>Widget_Feedback<br>Widget_Comments_Admin<br>Widget_Comments_Archive<br>Widget_Comments_Edit<br>Widget_Comments_Ping<br>Widget_Comments_Recent||
|filter|$value<br>$comments Widget_Abstract_Comments对象|以下句柄同样具有此接口:<br>Widget_Feedback<br>Widget_Comments_Admin<br>Widget_Comments_Archive<br>Widget_Comments_Edit<br>Widget_Comments_Ping<br>Widget_Comments_Recent||
|gravatar|$size<br>$rating<br>$default<br>$comments Widget_Abstract_Comments对象|以下句柄同样具有此接口:<br>Widget_Feedback<br>Widget_Comments_Admin<br>Widget_Comments_Archive<br>Widget_Comments_Edit<br>Widget_Comments_Ping<br>Widget_Comments_Recent||
|autoP|$text|以下句柄同样具有此接口:<br>Widget_Feedback<br>Widget_Comments_Admin<br>Widget_Comments_Archive<br>Widget_Comments_Edit<br>Widget_Comments_Ping<br>Widget_Comments_Recent||
|markdown|$text|以下句柄同样具有此接口:<br>Widget_Feedback<br>Widget_Comments_Admin<br>Widget_Comments_Archive<br>Widget_Comments_Edit<br>Widget_Comments_Ping<br>Widget_Comments_Recent||

### Widget_Abstract_Contents

|接口|参数|描述|
|:--|:--|:--|
|excerpt|text<br>$contents Widget_Abstract_Contents对象|以下句柄同样具有此接口:<br>Widget_Archive<br>Widget_Upload<br>Widget_XmlRpc<br>Widget_Contents_Related<br>Widget_Contents_Attachment_Admin<br>Widget_Contents_Attachment_Related<br>Widget_Contents_Attachment_Unattached<br>Widget_Contents_Page_List<br>Widget_Contents_Post_Admin<br>Widget_Contents_Page_Admin<br>Widget_Contents_Post_Edit<br>Widget_Contents_Attachment_Edit<br>Widget_Contents_Page_Edit<br>Widget_Contents_Post_Recent<br>Widget_Contents_Related_Author|
|excerptEx|$excerpt<br>$contents Widget_Abstract_Contents对象|以下句柄同样具有此接口:<br>Widget_Archive<br>Widget_Upload<br>Widget_XmlRpc<br>Widget_Contents_Related<br>Widget_Contents_Attachment_Admin<br>Widget_Contents_Attachment_Related<br>Widget_Contents_Attachment_Unattached<br>Widget_Contents_Page_List<br>Widget_Contents_Post_Admin<br>Widget_Contents_Page_Admin<br>Widget_Contents_Post_Edit<br>Widget_Contents_Attachment_Edit<br>Widget_Contents_Page_Edit<br>Widget_Contents_Post_Recent<br>Widget_Contents_Related_Author|
|content|text<br>$contents Widget_Abstract_Contents对象|以下句柄同样具有此接口:<br>Widget_Archive<br>Widget_Upload<br>Widget_XmlRpc<br>Widget_Contents_Related<br>Widget_Contents_Attachment_Admin<br>Widget_Contents_Attachment_Related<br>Widget_Contents_Attachment_Unattached<br>Widget_Contents_Page_List<br>Widget_Contents_Post_Admin<br>Widget_Contents_Page_Admin<br>Widget_Contents_Post_Edit<br>Widget_Contents_Attachment_Edit<br>Widget_Contents_Page_Edit<br>Widget_Contents_Post_Recent<br>Widget_Contents_Related_Author|
|contentEx|$content<br>$contents Widget_Abstract_Contents对象|以下句柄同样具有此接口:<br>Widget_Archive<br>Widget_Upload<br>Widget_XmlRpc<br>Widget_Contents_Related<br>Widget_Contents_Attachment_Admin<br>Widget_Contents_Attachment_Related<br>Widget_Contents_Attachment_Unattached<br>Widget_Contents_Page_List<br>Widget_Contents_Post_Admin<br>Widget_Contents_Page_Admin<br>Widget_Contents_Post_Edit<br>Widget_Contents_Attachment_Edit<br>Widget_Contents_Page_Edit<br>Widget_Contents_Post_Recent<br>Widget_Contents_Related_Author|
|isFieldReadOnly|$name|以下句柄同样具有此接口:<br>Widget_Archive<br>Widget_Upload<br>Widget_XmlRpc<br>Widget_Contents_Related<br>Widget_Contents_Attachment_Admin<br>Widget_Contents_Attachment_Related<br>Widget_Contents_Attachment_Unattached<br>Widget_Contents_Page_List<br>Widget_Contents_Post_Admin<br>Widget_Contents_Page_Admin<br>Widget_Contents_Post_Edit<br>Widget_Contents_Attachment_Edit<br>Widget_Contents_Page_Edit<br>Widget_Contents_Post_Recent<br>Widget_Contents_Related_Author|
|filter|$value<br>$contents Widget_Abstract_Contents对象|以下句柄同样具有此接口:<br>Widget_Archive<br>Widget_Upload<br>Widget_XmlRpc<br>Widget_Contents_Related<br>Widget_Contents_Attachment_Admin<br>Widget_Contents_Attachment_Related<br>Widget_Contents_Attachment_Unattached<br>Widget_Contents_Page_List<br>Widget_Contents_Post_Admin<br>Widget_Contents_Page_Admin<br>Widget_Contents_Post_Edit<br>Widget_Contents_Attachment_Edit<br>Widget_Contents_Page_Edit<br>Widget_Contents_Post_Recent<br>Widget_Contents_Related_Author|
|title|title<br>$contents Widget_Abstract_Contents对象|以下句柄同样具有此接口:<br>Widget_Archive<br>Widget_Upload<br>Widget_XmlRpc<br>Widget_Contents_Related<br>Widget_Contents_Attachment_Admin<br>Widget_Contents_Attachment_Related<br>Widget_Contents_Attachment_Unattached<br>Widget_Contents_Page_List<br>Widget_Contents_Post_Admin<br>Widget_Contents_Page_Admin<br>Widget_Contents_Post_Edit<br>Widget_Contents_Attachment_Edit<br>Widget_Contents_Page_Edit<br>Widget_Contents_Post_Recent<br>Widget_Contents_Related_Author|
|autoP|$text|以下句柄同样具有此接口:<br>Widget_Archive<br>Widget_Upload<br>Widget_XmlRpc<br>Widget_Contents_Related<br>Widget_Contents_Attachment_Admin<br>Widget_Contents_Attachment_Related<br>Widget_Contents_Attachment_Unattached<br>Widget_Contents_Page_List<br>Widget_Contents_Post_Admin<br>Widget_Contents_Page_Admin<br>Widget_Contents_Post_Edit<br>Widget_Contents_Attachment_Edit<br>Widget_Contents_Page_Edit<br>Widget_Contents_Post_Recent<br>Widget_Contents_Related_Author|
|markdown|$text|以下句柄同样具有此接口:<br>Widget_Archive<br>Widget_Upload<br>Widget_XmlRpc<br>Widget_Contents_Related<br>Widget_Contents_Attachment_Admin<br>Widget_Contents_Attachment_Related<br>Widget_Contents_Attachment_Unattached<br>Widget_Contents_Page_List<br>Widget_Contents_Post_Admin<br>Widget_Contents_Page_Admin<br>Widget_Contents_Post_Edit<br>Widget_Contents_Attachment_Edit<br>Widget_Contents_Page_Edit<br>Widget_Contents_Post_Recent<br>Widget_Contents_Related_Author|

### Widget_Abstract_Metas

|接口|参数|描述|
|:--|:--|:--|
|filter|$value<br>$metas Widget_Abstract_Metas对象|以下句柄同样具有此接口:<br>Widget_Metas_Category_Edit<br>Widget_Metas_Category_List<br>Widget_Metas_Category_Admin<br>Widget_Metas_Tag_Cloud<br>Widget_Metas_Tag_Admin<br>Widget_Metas_Tag_Edit|

### Widget_Abstract_Users

|接口|参数|描述|
|:--|:--|:--|
|filter|$value<br>$users Widget_Abstract_Users对象|以下句柄同样具有此接口:<br>Widget_Login<br>Widget_Logout<br>Widget_Register<br>Widget_Users_Admin<br>Widget_Users_Author<br>Widget_Users_Edit<br>Widget_Users_Profile|

### Widget_Comments_Archive

|接口|参数|描述|
|:--|:--|:--|
|listComments|singleCommentOptions<br>$archive Widget_Comments_Archive对象||
|reply|$word<br>$archive Widget_Comments_Archive对象||
|cancelReply|$word<br>$archive Widget_Comments_Archive对象||

### Widget_Comments_Edit

|接口|参数|描述|
|:--|:--|:--|
|mark|$comment<br>$edit Widget_Comments_Edit对象<br>$status||
|delete|$comment<br>$edit Widget_Comments_Edit对象||
|finishDelete|$comment<br>$edit Widget_Comments_Edit对象||
|edit|$comment<br>$edit Widget_Comments_Edit对象||
|finishEdit|$edit Widget_Comments_Edit对象||
|comment|$comment<br>$edit Widget_Comments_Edit对象||
|finishComment|$edit Widget_Comments_Edit对象||

### Widget_Contents_Attachment_Edit

|接口|参数|描述|
|:--|:--|:--|
|delete|$post<br>$edit Widget_Contents_Attachment_Edit对象||
|finishDelete|$post<br>$edit Widget_Contents_Attachment_Edit对象||
|delete|$post<br>$edit Widget_Contents_Attachment_Edit对象||
|finishDelete|$post<br>$edit Widget_Contents_Attachment_Edit对象||

### Widget_Contents_Page_Edit

|接口|参数|描述|
|:--|:--|:--|
|write|$contents<br>$edit Widget_Contents_Page_Edit对象||
|finishPublish|$contents<br>$edit Widget_Contents_Page_Edit对象||
|finishSave|$contents<br>$edit Widget_Contents_Page_Edit对象||
|delete|$page<br>$edit Widget_Contents_Page_Edit对象||
|finishDelete|$page<br>$edit Widget_Contents_Page_Edit对象||

### Widget_Contents_Post_Edit

|接口|参数|描述|
|:--|:--|:--|
|getDefaultFieldItems|$layout|以下句柄同样具有此接口:<br>Widget_Contents_Attachment_Edit<br>Widget_Contents_Page_Edit|
|write|$contents<br>$edit Widget_Contents_Post_Edit对象|以下句柄同样具有此接口:<br>Widget_Contents_Attachment_Edit<br>Widget_Contents_Page_Edit|
|finishPublish|$contents<br>$edit Widget_Contents_Post_Edit对象|以下句柄同样具有此接口:<br>Widget_Contents_Attachment_Edit<br>Widget_Contents_Page_Edit|
|finishSave|$contents<br>$edit Widget_Contents_Post_Edit对象|以下句柄同样具有此接口:<br>Widget_Contents_Attachment_Edit<br>Widget_Contents_Page_Edit|
|delete|$post<br>$edit Widget_Contents_Post_Edit对象|以下句柄同样具有此接口:<br>Widget_Contents_Attachment_Edit<br>Widget_Contents_Page_Edit|
|finishDelete|$post<br>$edit Widget_Contents_Post_Edit对象|以下句柄同样具有此接口:<br>Widget_Contents_Attachment_Edit<br>Widget_Contents_Page_Edit|

### Widget_Metas_Category_List

|接口|参数|描述|
|:--|:--|:--|
|listCategories|categoryOptions<br>$list Widget_Metas_Category_List对象|以下句柄同样具有此接口:<br>Widget_Metas_Category_Admin|
