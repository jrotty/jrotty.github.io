# Typecho前台注册

### 代码

```html
<form action="<?php $this->options->registerAction();?>" method="post" name="register" role="form">
<input type="hidden" name="_" value="<?php echo $this->security->getToken($this->request->getRequestUrl());?>">
用户名<input type="text" name="name">
邮箱:<input type="email" id="mail" name="mail" >
<button type="submit" name="loginsubmit" value="true">注册</button>
</form>
```

### 说明

用户进入注册页面，只会要求用户填写用户名和邮箱，点击注册按钮后会跳转到程序后台，此时会提示被分配了个临时密码，同时提示用户修改默认密码，填写个人信息如昵称，个人主页等。

### 扩展
如果也想像前台登录一样，登陆后自定义跳转页面，需要修改`/var/Widget/Register.php`这个文件，倒数第三行左右的这个代码`$this->response->redirect($this->options->adminUrl);`换成如下代码
```php
if (NULL != $this->request->referer) {
$this->response->redirect($this->request->referer);
} else{
$this->response->redirect($this->options->adminUrl);
}
```
这样在form里插入
```html
<input type="hidden" name="referer" value="跳转地址">
```
即可。