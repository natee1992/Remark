### 时区处理
python tzinfo
**时间排序**
```
# 对alert_list列表自定义排序字段
"alert_time" : "2018-07-03 13:39:58"
# 排序参数
alarm_list.sort(key=lambda x: x["alarmTime"], reverse=True)
```
### python中的groupby函数
python中itertools的groupby函数可以通过指定的key对list做分组
list按照分组字段必须是有序的
```python
for key,group in groupby(list_demo,lamda x:x['date']):
  print(key)
  print(group)
```
  
