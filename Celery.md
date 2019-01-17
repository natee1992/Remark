**定时任务**

```python
from datetime import timedelta as td
from celery.schedules import crontab

# 导入指定的任务模块
CELERY_IMPORTS = {
    'celery_app.task1',
    'celery_app.task2'
}

CELERY_TIMEZONE = 'Asia/Shanghai'

CELERYBEAT_SCHEDULE = {
    'task1' = {
        'task': 'celery_app.task1.add',
        'schedule': td(seconds=10),  # 每10s执行一次
        'args': (2,3)       # 任务参数
    }
    'task2' = {
        'task': 'celery-app.taks2.mdd',
        'schedule': crontab(hour=19,minute=20),    # 每天19点20分执行
        'args': (2,3)    
    }
}

```
启动命令

> celery beat -A worker -l 

