# Laravel表单验证 #

对于表单验证，大致有三种形式，

1. 直接在Controller获得Input(Input只是对Request的封装), 
2. 把验证封装在Model里，
3. 通过FormRequest，

假设有一个用户注册的表单需要验证, 表结构如下，

```
	User{
		name,
		email,
		password,
	}	
```

### 直接在Controller获得Input：###

```
	$input = [
        'name' => Input::get('name'),
        ‘email’ => Input::get('email'),
        'password' => Input::get('password'),
    ];        
    $validator = Validator::make($input, [
        'name' => 'required|max:255',
        'email' => 'required|email|max:255|unique:user',
        'password' => 'required|confirmed|min:4',
    ]);

    if ($validator->fails())
    {
        return redirect(action('UserController@getSignup'))
            ->withErrors($validator)
            ->withInput();
    }
```
这种方式直接可以在Controller里查看，比较直观，可以比较自由地进行操作传入的参数,

不过可能会造成Controller里面代码过多。

### 把验证封装在Model里 ###
```
	class User extends Eloquent
	{
	    private $rules = array(
	        'name' => 'required|max:255',
	        'email' => 'required|email|max:255|unique:user',
	        'password' => 'required|confirmed|min:4',
	    );

	    private $errors;

	    public function validate($data)
	    {
	        $v = Validator::make($data, $this->rules);
	        if ($v->fails())
	        {
	            $this->errors = $v->errors;
	            return false;
	        }

	        return true;
		}

		public function errors()
	    {
	        return $this->errors;
	    }
	}
	
```
这种方式把验证方式封装在Model里面，在Controller里面进行验证的时候不需要关注太多里面的细节，

缺点就是不太直观, 而且会造成Model里面代码过多。

### 通过FormRequest：###

通过```php artisan make:request SignupRequest ```

创建一个FormRequest子类到app/Http/Requests里

```
	namespace App\Http\Requests;

	use App\Http\Requests\Request;
	use Illuminate\Contracts\Validation\Validator;

	class SignupRequest extends Request
	{
	    
	    public function authorize()
	    {
	        return true;
	    }
	    
	    public function rules()
	    {
	        return [
	            'name' => 'required|max:255',
		        'email' => 'required|email|max:255|unique:user',
		        'password' => 'required|confirmed|min:4',
	        ];
	    }

	    protected function formatErrors(Validator $validator)
	    {
	        return $validator->errors()->all();
	    }
	}

```

这样做的好处呢, 就是Controller里面的只需要处理正确的逻辑，不需要再管表单验证的逻辑，

但是这样会造成那些规范化的表单需要生成较多的FormRequest，而且对于对象的新建和编辑则需要

创建两个，如果不想这样可以使用在验证规则里添加*sometimes*

*注意事项*：

1. 引入Validator，要通过上面的引入方式，而不是像在Controller里面的```use Validator```
2. 上面的authorize()方法，一般情况下返回true即可，如果想要authorize某个对象，则修改里面的代码，如果返回flase,则默认返回403
3. 可以在formatErrors()方法里面重写错误提示


另外，对于```$this->throwValidationException( $request, $validator );```

是```Illuminate\Foundation\Validation\ValidatesRequests```里面的方法，
```protected function throwValidationException(Request $request, $validator)```

直接看源码中涉及到的两个方法，
```
	protected function throwValidationException(Request $request, $validator)
    {
        throw new HttpResponseException($this->buildFailedValidationResponse(
            $request, $this->formatValidationErrors($validator)
        ));
    }

	protected function buildFailedValidationResponse(Request $request, array $errors)
    {
        if ($request->ajax() || $request->wantsJson()) {
            return new JsonResponse($errors, 422);
        }

        return redirect()->to($this->getRedirectUrl())
                        ->withInput($request->input())
                        ->withErrors($errors, $this->errorBag());
    }
```
如果对这个不理解的，现在应该知道了，其实就是对我们的正常写法的封装而已。


### 总结 ###

其实所有的方式都是大同小异，需要编写的验证规则都是必不可少的，

既然如此，我们推荐使用_第一种方式_，尽量使代码的简单明了。
