<a href='#shiqu'>时区处理</a>
<a href='#groupby'>groupby</a>
<a href='#dict_to_json'>dict输出成json</a>


### 时区处理
<p id='shiqu'></p>
python tzinfo
**时间排序**
```
# 对alert_list列表自定义排序字段
"alert_time" : "2018-07-03 13:39:58"
# 排序参数
alarm_list.sort(key=lambda x: x["alarmTime"], reverse=True)
```
### python中的groupby函数
<p id='groupby'></p>
python中itertools的groupby函数可以通过指定的key对list做分组
list按照分组字段必须是有序的
```python
for key,group in groupby(list_demo,lamda x:x['date']):
  print(key)
  print(group)
```
  
### dict输出成json
<p id='dict_to_json'></p>

> 字典合并

```python
d1 = {1:2}
d2 = {3:4}
d3 = dict(d1,**d2)


```
> 字典合并

```python
improt json
d = {}
d_str = json.dumps(d)
with open('output.json','w+') as writer:
  writer.wirte(d)
 
```
