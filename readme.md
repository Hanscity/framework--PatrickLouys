# About Framework

这个框架的内容来自 https://github.com/PatrickLouys/no-framework-tutorial，一步步的演习而来。

直到第八章容器注入，完结。第九章，我的有错误，就没有往下走了。

首先，是基于 composer 的，这个是必须的

这里面有报错机制，我没有深入看 Whoop 包

这里面有路由，是将 uri 进行截取，然后找到相关的类，大概如此

这里面将采取完整的面向对象，所以将 http 请求中的变量提取保存到对象中，用对象来操作。
如果是用 $_SERVER 等全局变量，那面向对象就不是那么的纯粹。
对于这一点，我保持中立的态度吧。

这个思想代表性的作品是 Symfony 的 http 组件，也是如此。它举了一个例子，获取用户的 Ip，
在不同的情况下，有不同的变化。如果用组件中的方法，就可以省去很多的变化，直接调用即可。这样说，
也是可以的。

这里面有依赖注入的容器，到目前为止，我是不敢苟同的，至少在下面的用法和思想中。

```
//注入的文件
$injector->alias('Http\Request', 'Http\HttpRequest');
$injector->share('Http\HttpRequest');
$injector->define('Http\HttpRequest', [
    ':get' => $_GET,
    ':post' => $_POST,
    ':cookies' => $_COOKIE,
    ':files' => $_FILES,
    ':server' => $_SERVER,
]);

//生成类
$request = $injector->make('Http\Request');

//具体的使用
$request->getPath();


```

这是此 Http 的接口文件
```
<?php

namespace Http;

interface Request
{
    public function getParameter($key, $defaultValue = null);
    public function getQueryParameter($key, $defaultValue = null);
    public function getBodyParameter($key, $defaultValue = null);
    public function getFile($key, $defaultValue = null);
    public function getCookie($key, $defaultValue = null);
    public function getParameters();
    public function getQueryParameters();
    public function getBodyParameters();
    public function getRawBody();
    public function getCookies();
    public function getFiles();
    public function getUri();
    public function getPath();
    public function getMethod();
    public function getHttpAccept();
    public function getReferer();
    public function getUserAgent();
    public function getIpAddress();
    public function isSecure();
    public function getQueryString();
}


```

以上是依赖注入的代码，大概如此。它的思想是假如有一天，你不想用 Http\HttpRequest，换成 
Http\AnotherHttpRequest,只需要改注入文件就好。这真是太天真了。
具体使用中的 ```$request->getPath();``` 在 AnotherHttp 包中
很可能使用的是 ```$request->getPathInfo();``` ，你始终逃不掉要修改这些具体业务上的代码。
上面的接口文件，又没有啥权威的机构来定义规范，肯定是大同小异，各不相同。
除非，你根据上面的接口文件，重新写一个自定义的 Composer 包，这也是需要一定的修改的。

你想换一个包，肯定是麻烦的，不可能就修改这几句代码。所以，为什么建议你用广泛流传的包，就是不
希望走到换个包的窘境。

这只是关于依赖容器的一个角度，在其它的场景中，应该有一定的用处，特别是接口类来自自己的定义。
我们拭目以待吧~



