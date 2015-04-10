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












