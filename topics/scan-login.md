# 微信扫码登陆实现

同事反馈
---------------------------------------------------------------------------

小林今年先后两次反映帐号密码登陆存在的隐患：

> 2023-03-27
>
> 还有刘东亮、吴银叶、陈齐龙，这些人得保证能下载的人，
>
> 只要不是初始密码就行。我都可以等观鹏、邹总的账户
>
> 能不能强制这些更改初始密码啊[破涕为笑]
>
> 比如我的账号，权限可大，啥都能下载
> 
> 2023-05-06
>
> 陈奎，咱现在使用系统的人有没有不改密码的呀？是不是上次咱俩沟通完，大家都改了
>
> 会有可能别的公司通过盗取咱的IP 破解密码么
>
> 有个客户说，有人去他那准备报出来他去年从咱公司的采购规格、数量

自己用了两天时间实现此功能。

思路记录
---------------------------------------------------------------------------

流程大致如下：

- 用户通过浏览器进入登陆页面，此时自有 server 生成一个 session id;
- 用此 session id 作为参数生成一个二维码；
- 用户扫码，微信服务器向我们的服务器回调地址发送扫码事件；
- 回调逻辑处理业务：微信服务器发送过来的有 session id, 还有扫码用户的 openId, 从 user 表内查找匹配此 openId 的用户，找到后，将此会话的 `is_login` 变量设置为 1, 同时使用 `setIdentity()` 设置登陆态；
- 浏览器 client 借助 ajax 每个5秒发送查询当前会话 `is_login` 的值，当值变成 1 时，视为登陆成功，client 将页面跳转至首页；

### 二维码

这里使用的是公众号“[帐号管理 带参数二维码][wechat-doc-qrcode]”功能。用户扫码后，会触发 SCAN 事件，微信服务器将此事件报告给我们自己服务器上的回调地址，根据传送过来的信息，我们可以在服务器上实现具体的业务逻辑。

微信提供的二维码有两种：临时的和永久的，就扫码登陆这个场景来说，临时二维码适合。

### server 端更改会话变量值不生效的问题

起初怀疑 www-data 没有权限更改会话变量，查询 `/var/lib/php/sessions/` 目录下，发现会话的权限没有问题。我又新建了一个 `test/bak` 页面，在那里手动更改变量的值，再在 `test/index` 上查询该变量的值，的确是存储上了， 所以问题出在扫码调用设置 session 环节.

最后发现业务代码设置的会话并不是浏览器中的会话！难怪不起作用，现在想来，这个会话应该标记的是腾讯服务器,因为是它向回调地址发送了请求。于是使用 `$session->setId()` 显性改为浏览器会话。

```php
$session = Yii::$app->session;
if (!$session->isActive) {
    $session->open();
}

// 这里不能用 $session->id = $sessionId; 因为
$session->setId($sessionId);

$session->set('is_login', 1);

// 这个 close() 很关键，表示保存对变量的更改，缺少相当于不保存
$session->close();

// 设置登陆态
Yii::$app->user->switchIdentity($user, 60);
```


[wechat-doc-qrcode]: https://developers.weixin.qq.com/doc/offiaccount/Account_Management/Generating_a_Parametric_QR_Code.html
