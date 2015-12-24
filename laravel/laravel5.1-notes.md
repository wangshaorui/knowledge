# Laravel5.1 基础

[官方文档](https://laravel.com/docs/5.1)

## Route

路由设置文件位于 `app/Http/routes.php`

### get / post / put / delete
```
Route::get('/user', function () {
	// do something 
}
```

### 路由参数
```
Route::get('/user/{id}', function ($id) {
	// do something
}
```

默认参数

```
Route::get('/user/{id?}', function ($id = 1) {
	// do something
}
```

## Model
创建的 `Model` 文件位于 App 下, 继承 
`use Illuminate\Database\Eloquent\Model`， 使用 `Larval` 自带的 `ORM` `Eloquent`

### 命名规则

表名复数， Model 文件名 / Model Class 名单数,首字母大写

**自定义:**

* `protected $table` 指定表名, 默认类名小写复数
* `$primaryKey` 指定主键，默认 id
* `$timestamps = false` 取消对 `created_at` / `updated_at`的管理, 默认保存时更新 `updated_at`
* `$connection = 'connection-nama' ` 指定使用的数据库连接, 默认使用 默认数据库连接


```
use Illuminate\Database\Eloquent\Model;

class Customer extends Model {
	protected $table = 'other_customers';
}
```

此时 `Customer` 已经自带继承的很多方法

### Query Builder

* all() : 获取该表的所有查询结果
* get() : 获取该查询的所有结果，可传入一个字符串数组指定返回的列
* find($id)
* findOrFail($id) : 找不到抛出`Not Found Exceptions`错误,并返回`404`
* where('name', '=', 'Tony'): 默认的 '=' 可忽略
* where(...)->first() : 获取查找到的第一个结果
* where(...)->value('name'):  获取查找到的第一个结果的指定列的值
* lists('colume name') : 返回指定列数据的一个数组
* lists('id', 'name') : 放回一个指定的关联数组

### insert

simply create a new model instance, set attributes on the model, then call the `save` method:

```
$customer = new Customer;
$customer->name = 'Tony';
$customer->save()
```

### update
#### update instance
To update a model, you should retrieve it, set any attributes you wish to update, and then call the save method


```
$customer = App\Customer::find(1);
$customer->name = 'new name';
$customer->save();
```
#### update use update()

```
App\Customer::where('age', '<', 20)->update(['age' => 20]}
```

### mass assiginment

#### create
使用 `create` 插入数据

需要设置 `Model` 的 `$fillable` 和 `$guarded` 属性来允许可以设置  / 禁止设置的列，二者择一

```
public $fillable = ['name'] // 只允许设置 name
public $guarded = ['name'] // 除了 name 之外的都允许设置
```


```
$flight = App\Flight::create(['name' => 'Flight 10']);
```


#### Other Creation Methods
```
// Retrieve the flight by the attributes, or create it if it doesn't exist...
$flight = App\Flight::firstOrCreate(['name' => 'Flight 10']);

// Retrieve the flight by the attributes, or instantiate a new instance...
$flight = App\Flight::firstOrNew(['name' => 'Flight 10']);
```

### Deleting An Existing Model By Key

#### retrieving the model and delete

```
$flight = App\Flight::find(1);

$flight->delete();
```

#### directly delete without retrieving

```
App\Flight::destroy(1);

App\Flight::destroy([1, 2, 3]);

App\Flight::destroy(1, 2, 3);
```

#### Soft Deleting 软删除

删除时标记 `deleted_at` 为操作时间，并不真的删除 

If a model has a `non-null` deleted_at value, the model has been soft deleted

`Model` need use the `Illuminate\Database\Eloquent\SoftDeletes `trait on the model and add the `deleted_at` column to your `$dates` property:


```
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class Flight extends Model
{
    use SoftDeletes;
    protected $dates = ['deleted_at'];
}
```

to determine if a given model instance has been `soft deleted`, use the `trashed` method:

```
if ($flight->trashed()) {
    //
}
```

### Querying Soft Deleted Models

using the `withTrashed` method on the `query`

```
$flights = App\Flight::withTrashed()
                ->where('account_id', 1)
                ->get();
```     
         

#### Where Clause Caveats
使用 `SoftDelete` 之后 `where` 语句需要使用 `advance where clauses`，这点很不方便

When adding orWhere clauses to your queries on soft deleted models, always use `advance where clauses` to logically group the WHERE clauses. For example:

```
User::where(function($query) {
        $query->where('name', '=', 'John')
              ->orWhere('votes', '>', 100);
        })
        ->get();
```

#### Retrieving Only Soft Deleted Models
 
 ```
 $flights = App\Flight::onlyTrashed()
                ->where('airline_id', 1)
                ->get();
 ```
 
### Restoring Soft Deleted Models

```
$flight->restore();
```

```
App\Flight::withTrashed()
        ->where('airline_id', 1)
        ->restore();
```

### 分块获取并处理数据


```
chunkSize = 100
Customer::all()->chunk(chunkSize, function($customers) {
	foreach （$customers as $customer) {
		// do something
	}
	// return false  you can cancel further chunk handle
}
```

### Aggregates

* count()
* min('price')
* max('price')
* avg('price')
* sum('price')
 

### Selects

* select('col1', 'col2')->get() : 指定返回的列
* distinct() ： 移除重复列

**addSelect**

```
$query = DB::table('users')->select('name');

$users = $query->addSelect('age')->get();
```

### joins

**innerjoin**

```
$users = DB::table('users')
            ->join('contacts', 'users.id', '=', 'contacts.user_id')
            ->join('orders', 'users.id', '=', 'orders.user_id')
            ->select('users.*', 'contacts.phone', 'orders.price')
            ->get();
```

**leftjoin**

```  
            $users = DB::table('users')
            ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
            ->get();
```

**advanced join**


```
DB::table('users')
        ->join('contacts', function ($join) {
            $join->on('users.id', '=', 'contacts.user_id')->orOn(...);
        })
        ->get();
```
 
 
### unions
 
 
 ```
 $first = DB::table('users')
            ->whereNull('first_name');

$users = DB::table('users')
            ->whereNull('last_name')
            ->union($first) // or unionAll($first)
            ->get();
 ```
 
### Where

**Or Statements**

```
$users = DB::table('users')
                    ->where('votes', '>', 100)
                    ->orWhere('name', 'John')
                    ->get();
```

#### whereBetween

```
$users = DB::table('users')
->whereBetween('votes', [1, 100])->get();
```
* whereNotBetween
* whereIn
* whereNotIn
* whereNull
* whereNotNull

### Advanced Where Clauses

如果复数条件就传入 `function`，参数为 `$query` 在此参数上添加条件

####  Parameter Grouping

```
DB::table('users')
            ->where('name', '=', 'John')
            ->orWhere(function ($query) {
                $query->where('votes', '>', 100)
                      ->where('title', '<>', 'Admin');
            })
            ->get();
```

#### Exists Statements

```
DB::table('users')
            ->whereExists(function ($query) {
                $query->select(DB::raw(1))
                      ->from('orders')
                      ->whereRaw('orders.user_id = users.id');
            })
            ->get();
```

#### Ordering

* orderBy('name', 'desc') // 'asc'

#### groupBy / having / havingRaw

```
$users = DB::table('users')
                ->groupBy('account_id')
                ->having('account_id', '>', 100) // havingRaw('sum(price) > 2500')
                ->get();
```

#### skip / take 

* skip() -> offset : 跳过指定数量
* take() -> limit : 获取指定数量

### insert()

```
DB::table('users')->insert([
    ['email' => 'taylor@example.com', 'votes' => 0],
    ['email' => 'dayle@example.com', 'votes' => 0]
]);
```

#### insertGetId()

```
$id = DB::table('users')->insertGetId(
    ['email' => 'john@example.com', 'votes' => 0]
);
```

### updates

```
DB::table('users')
            ->where('id', 1)
            ->update(['votes' => 1]);
```

### increment / decrement

```
DB::table('users')->increment('votes');

DB::table('users')->increment('votes', 5);

DB::table('users')->decrement('votes');

DB::table('users')->decrement('votes', 5);

You may also specify additional columns to update during the operation:

DB::table('users')->increment('votes', 1, ['name' => 'John']);
```

#### deletes

```
DB::table('users')->delete();
```

#### truncate

```
DB::table('users')->truncate();
```

## Contorller

创建控制器，创建的控制器位于 `App/Http/Controller` 下

```
php artisan make:controller CustomerController
```

路由绑定控制器

在 routes.php 中，使用 `@` 连接 `class` 和 `method`

```
Route::get('/user/{id}', 'CustomerController@show')
```

## requests

### 获取变量值

`$request->input('name')`

`$request->name`

##### 若不存在返回默认值

`$name = $request->input('name', 'default')`

##### 获取放回的数组值

`$name = $request->input('products.0.name')`

##### 判断变量是否存在

`$request->has('name')`

##### 获取所有数据

`$input = $request->all()`

##### 获取指定的数据

`$input = $request->only(['user', 'password'])`

`$input = $request->only('user', 'password')`

#####  获取除了不需要的数据外的所有数据

`$input = $request->except(['noneed'])

`$input = $request->except('noneed')

### 获取当前 url 信息

`$request->path()` : 路径名

`$request->url()` : 完整的 URL

`$request->is('admin/*') : 当前 URL 是否匹配 `admin/*`

### 获取当前方法

`$request->method()` 

`$request->isMethod('post')` : 判断是否是某方法

### 保存旧的输入

##### Flashing Input To The Session


```
$request->flash();

$request->flashOnly('username', 'password')

$request->flashExcept('noneed')
```

##### Flash Input Into Session Then Redirect


```
return redirect('form')->withInput();

return redirect('form')->withInput($request->except('password'));
```

### 获取旧的输入

```
$username = $request->old('username');

// in view template
{{ old('username') }}
```

### Cookie

#### get cookie

`$value = $request->cookie('name');`

#### set cookie

```

$response = new Illuminate\Http\Response('Hello World');

$response->withCookie(cookie('name', 'value', $minutes));

return $response;

$response->withCookie(cookie()->forever('name', 'value'));
```

## response

### 示例
```
return response($content, $status)->header('Content-Type', $type)
                 ->withCookie('name', 'value');
```

### JSON

```
return response()->json(['name' => 'Abigail', 'state' => 'CA']);
```

### JSONP

```
return response()->json(['name' => 'Abigail', 'state' => 'CA'])
                 ->setCallback($request->input('callback'));
```

### File Downloads


```
return response()->download($pathToFile);

return response()->download($pathToFile, $name, $headers);
```

### Redirects

```
return redirect('home/dashbord');
```

#### 提交表单失败，带着提交信息返回

```
return back->withInput();
```

#### Redirecting To Named Routes 跳转到其他路由

直接跳转

```
return redirect()->route('login');
```

带路由参数

```
// for a route : prfile/{id}
return redirect()->route('profile', [$value]);

若 $value 是个 orm 对象，可以直接传递而不用 $value->id ，laravel 会自动提取
```

#### 跳转到其他控制器

```
return redirect()->action('HomeController@index');
```

带参数

```
return redirect()->action('UserController@profile', [1]);
传入的是控制器的第二个参数，第一个 request 自动传递
```
## Auth

[5.1见官网示例](https://laravel.com/docs/5.1/authentication)

5.2 的验证只需要 `php artisan make:auth`

## middleware

### 创建
使用 `php artisan make:middleware Admin` 创建 `Admin middleware`

### 注册

位于 `App/Kernel.php` 中的 `App\Http\Kernel Class` 下添加


```

protected $routeMiddleware = [
    'auth' => \App\Http\Middleware\Authenticate::class,
    'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
    'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
    'yourmiddleware' => \Illuminate\Routing\Middleware\YourMiddleware::class,
];
```

### 使用


```
Route::get('/home', ['middleware' => 'auth', 'CustomerController@home'])

Route::get('/dashbord', ['middleware' => ['auth', 'yourmiddleware'], 'CustomerController@home'])

```

or 

```
Route::get('/', function () {
    //
})->middleware(['first', 'second']);
```

## Migaration

### 创建表 

`php artisan make:migration create_customer_table`

再 `database/migrations` 下生成对应的 `migration file`

`php artisan migrate` 创建对应表，对应`up()`函数

```
    public function up()
    {
        Schema::create('foods', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->unsignedInteger('price');
            $table->timestamp('created_at');
            $table->timestamp('updated_at');
        });
    }
```


`php artisan migrate:rollback` 回滚，对应`down()`函数



```
Schema::drop('foods');
```

## Seeder

创建对应的随机数据

### 创建 Factory

创建对应的 `factory` 来生成假冒的数据,文件位于 `database/factoies` 下

```
$factory->define(App\Food::class, function (Faker\Generator $faker) {
    return [
        'name' => $faker->name,
        'price' => random_int(1, 50),
    ];
});
```

### 创建 Seeder 文件

`php artisan make:seeder FoodTableSeeder`, 位于 `database/seeds` 下。用于生成并插入假冒数据

```
    public function run()
    {
	     createNums = 50;
        factory(\App\Customer::class, createNums)->create();
    }
}
```

### 再 DatabaseSeeder.php 中引用

```
    public function run()
    {
        Model::unguard();

        $this->call(FoodTableSeeder::class);

        Model::reguard();
    }

```

### 调用 artisan 生成 seed

```
php artisan db:seed
```

生成 `seed` 并插入数据库

### 重建数据库并插入 seed

`php artisan migrate:refresh --seed`

会回滚数据库并重新建表插入 `seed`