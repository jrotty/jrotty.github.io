# typecho常量

### debug模式
做第三方开发必须打开的，报错信息会更加详细，方便找到开发错误点。
在`config.inc.php`最后面插入`define('__TYPECHO_DEBUG__', true);`。

### 后台路径自定义
后台路径字定义，用于修改程序后台地址，提高安全性。
**typecho**默认后台地址是**admin**，如果我们想改成**typecho-admin**，那么首先登陆服务器把**admin**文件夹名字改成**typecho-admin**。
然后在`config.inc.php`找到`define('__TYPECHO_ADMIN_DIR__', '/admin/');`将其中的`admin`改成`typecho-admin`即可。

### 强制https
当你用**https**的时候**typecho**会自动识别的，但是因为大家的环境千差万别，会有识别错误的情况，比如你**https**都布置好了，但是程序依旧只会加载**http**的，所以该常量的作用就是强制程序使用**https**，解决识别错误的情况。
用法
在`config.inc.php`最后面插入`define('__TYPECHO_SECUER__', true);`。

### 公共头像地址替换

**typecho**用的**GRAVATAR**的公共头像地址，**GRAVATAR**的服务器在美国，虽然它在全世界做镜像，但是在国内依旧很慢。然后发现有国内有很多人为其做反向代理，把头像缓存到本地，来提高速度，以前大家可能会使用插件来替换**typecho**的**GRAVATAR**的默认地址，实际上不需要，我们用这个常量就可以了，比如我们替换成`https://gravatar.cat.net/avatar/`，用法如下
用法
在`config.inc.php`最后面插入`define('__TYPECHO_GRAVATAR_PREFIX__', 'https://gravatar.cat.net/avatar');`
这样一来**typecho**就会替换成`https://gravatar.cat.net/avatar/`渠道的头像地址。【有些模板可能不支持】

### 自定义上传附件目录

可以单独定义一个不在typecho下的附件上传地址
存储到这里有什么用呢？
在`config.inc.php`最后面插入

```
define('__TYPECHO_UPLOAD_DIR__', '/www/static');
define('__TYPECHO_UPLOAD_URL__', 'https://xxxxx.upyun.com');
```

你可以给他单独一个反向代理，就是cdn，七牛，又拍云等cdn可以访问到的地址，然后通过反向代理把你这个路径抓取的，抓取过去后，你那前面的展示地址就可以换成你的cdn地址了,比如上述代码中`https://xxxxx.upyun.com`。
