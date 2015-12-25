### celery and flower ###

1. 在指定环境下安装celery: pip install celery
2. 在制定环境下安装flower: pip install flower
3. 编写相关的celery任务，相关操作可查看：[First Steps with Celery](http://celery.readthedocs.org/en/latest/getting-started/first-steps-with-celery.html)
3. 启动celery和flower: 
	1. celery -A proj worker --loglevel=info
	2. celery flower -A proj --port=5555
4. 查看flower的相关调用： [flower REST API](http://nbviewer.ipython.org/github/mher/flower/blob/master/docs/api.ipynb)
5. 任务查看: 访问http://1localhost:5555/
6. 注意事项: (如果用虚拟环境配置的，则localhost对应多地址是虚拟地址，比如：192.168.10.10)