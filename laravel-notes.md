# laravel-notes

标签（空格分隔）： laravel

---

## Laravel Eloquent
Eloquent 是 Laravel 的 'ORM'，即 'Object Relational Mapping'，对象关系映射。ORM 的出现是为了帮我们把对数据库的操作变得更加地方便。
### Eloquent 基础使用要点
1. 每一个继承了 Eloquent 的类都有两个 '固定用法' 'Article::find($number)' 'Article::all()'，前者会得到一个带有数据库中取出来值的对象，后者会得到一个包含整个数据库的对象合集。

2. 所有的中间方法如 'where()' 'orderBy()' 等都能够同时支持 '静态' 和 '非静态链式' 两种方式调用，即 'Article::where()...' 和 'Article::....->where()'。

3. 所有的 '非固定用法' 的调用最后都需要一个操作来 '收尾'，如：'->get()' 和 '->first()'。

##Laravel的几个扩展包
###mikemclin/laravel-wp-password
这是用来创建和验证 WordPress 的密码哈西。就是说， WordPress 里面的密码哈西可以导入 Laravel 来验证。

###frozennode/administrator
利用强大的 Eloquent ORM，加上简单的配置信息，快速生成数据管理后台。

###erusev/parsedown
这是PHP中解析markdown格式的扩展包。

###ceesvanegmond/minify
这个扩展包可以压缩项目中的 stylessheet 和 javascript 文件。


##HTTP中间件

HTTP 中间件提供一个方便的机制来过滤进入应用程序的 HTTP 请求，例如，Laravel 默认包含了一个中间件来检验用户身份验证，如果用户没有经过身份验证，中间件会将用户导向登录页面，然而，如果用户通过身份验证，中间件将会允许这个请求进一步继续前进。

###Before / After 中间件
在一个请求前后指定某个中间件取决于这个中间件自身。
###注册中间件
####全局中间件

若是希望中间件被所有的 HTTP 请求给执行，只要将中间件的类加入到 app/Http/Kernel.php 的 $middleware 属性清单列表中。

####指派中间件给路由

如果你要指派中间件给特定的路由，你得先将中间件在 app/Http/Kernel.php 配置一个键值，默认情况下，这个文件内的 $routeMiddleware 属性已包含了 Laravel 目前配置的中间件，你只需要在清单列表中加上一组自定义的键值即可。 中间件一旦在 HTTP kernel 文件内被定义，你即可在路由选项内使用 middleware 键值来指派：

    Route::get('admin/profile', ['middleware' => 'auth', function()
    {
        //
    }]);

##PHP 依赖包
在composer.json中配置好相关的包信息后，进行`composer update`，就可以自动安装包到项目中。
**注意**引用包的位置，前面加一个`\`，如下：

    $result = \Parsedown::instance()->parse($contents);

意思是全局找，否则在当前命名空间找会找不到。
或者也可以在前面加上use ...

##控制器往视图传数据
简单的如

    view('layout')->with('name',$name);

可以传单个变量，如果需要多个变量，可以传一个数组，就是这样：

    view('layout',['name' => 'wang', 'age' => '25'])
利用compact()函数就是这样了：
   
    view('layout',compact('name','age','gender'));
    











