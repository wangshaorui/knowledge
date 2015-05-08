# laravel-command

Laravel 自定义命令实现

http://laravel.com/docs/4.2/commands

## 

按照官方文档的 

php artisan command:make AssignUsers --command=users:assign

的编写完实现之后

还需要在

app/start/artisan.php 

中注册命令

Artisan::resolve('{命令类名}');