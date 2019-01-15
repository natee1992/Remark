<a href='#shiqu'>时区处理</a>

<a href='#groupby'>groupby</a>

<a href='#dict_to_json'>dict输出成json</a>

<a href='#readfile'>读取大文件及扩展</a>

---
### 时区处理
<p id='shiqu'></p>
python tzinfo

---

**时间排序**
```
# 对alert_list列表自定义排序字段
"alert_time" : "2018-07-03 13:39:58"
# 排序参数
alarm_list.sort(key=lambda x: x["alarmTime"], reverse=True)
```

---

### python中的groupby函数

<p id='groupby'></p>

python中itertools的groupby函数可以通过指定的key对list做分组
list按照分组字段必须是有序的

```python
for key,group in groupby(list_demo,lamda x:x['date']):
    print(key)
    print(group)
```

---

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
---

### 读取大文件及扩展
<p id='readfile'></p>

> 使用with上下文管理器方法，系统自动设置缓冲区

```python
with open(file,'r') as f:
    f.read()
```

> 使用chunk size 结合生成器

```python
def read_chunks(file,chunk_size=1024*85):
    """
    默认大小为1024*85，macos实测在文件大小为900Mb时最快
    """
    while 1:
        data = file.read(chunk_size)
        if not data:
            break
        yield data

if __name__ == '__main__':
    for r in read_chunks(file):
        process(r)  # 文件对象操作
```

> 使用mmap读写

**mmap的概念**

Linux文件系统的三层结构想必大家多少都了解一些，每个进程中都有一个用户文件描述符表，表项指向一个全局的文件表中的某个表项，文件表表项有一个指向内存inode的指针，每个inode唯一标识一个文件。如果同时有多个进程打开同一文件，他们的用户文件描述符表项指向不同的文件表项，但是这些文件表项会指向同一个inode。

此时又会引出另外一个东东：page cache。内核会为每个文件单独维护一个page cache，用户进程对于文件的大多数读写操作会直接作用到page cache上，内核会选择在适当的时候将page cache中的内容写到磁盘上（当然我们可以手工fsync控制回写），这样可以大大减少磁盘的访问次数，从而提高性能。Page cache是linux内核文件访问过程中很重要的数据结构，page cache中会保存用户进程访问过得该文件的内容，这些内容以页为单位保存在内存中，当用户需要访问文件中的某个偏移量上的数据时，内核会以偏移量为索引，找到相应的内存页，如果该页没有读入内存，则需要访问磁盘读取数据。为了提高页得查询速度同时节省page cache数据结构占用的内存，linux内核使用树来保存page cache中的页。

在了解了以上的基础之后，我们就来比较一下mmap和read/write的区别，先说一下read/write系统调用，read/write系统调用会有以下的操作：

```
访问文件，这涉及到用户态到内核态的转换
读取硬盘文件中的对应数据，内核会采用预读的方式，比如我们需要访问100字节，内核实际会将按照4KB(内存页的大小)存储在page cache中
将read中需要的数据，从page cache中拷贝到用户缓冲区中

```
整个过程还是比较艰辛的，基本上涉及到用户内核态的切换，还有就是数据拷贝接下来继续说mmap吧，mmap系统调用是将硬盘文件映射到用内存中，说的底层一些是将page cache中的页直接映射到用户进程地址空间中，从而进程可以直接访问自身地址空间的虚拟地址来访问page cache中的页，这样会并涉及page cache到用户缓冲区之间的拷贝，mmap系统调用与read/write调用的区别在于：

```
mmap只需要一次系统调用，后续操作不需要系统调用
访问的数据不需要在page cache和用户缓冲区之间拷贝
```

从上所述，当频繁对一个文件进行读取操作时，mmap会比read高效一些。

 

   最后再说一下page cache的话题，从上面所说我们从磁盘文件中读取的内容都会存在page cache中，但当我们关闭这个文件时，page cache中内容会立马释放掉吗？答案是否，磁盘的读取速度比内存慢太多，如果能命中page cache可以显著提升性能，万一后续又有对这个文件的操作，系统就可以很快速的响应。当然，这些文件内容也不是一直存在page cache中的，一般只要系统有空闲物理内存，内核都会拿来当缓存使用，但当物理内存不够用，内存会清理出部分page cache应急，这也就是告诉我们程序对于物理内存的使用能省则省，交给内核使用，作用很大。

 

   还有就是普通的write调用只是将数据写到page cache中，并将其标记为dirty就返回了，磁盘I/O通常不会立即执行，这样做的好处是减少磁盘的回写次数，提供吞吐率，不足就是机器一旦意外挂掉，page cache中的数据就会丢失。一般安全性比较高的程序会在每次write之后，调用fsync立即将page cache中的内容回写到磁盘中。

*代码实现*
```python
import mmap
import time
'''
2.91
'''
# a = time.time()
# w = open('dd.json', 'w+b')
# with open('dialog_extract_data.json', "r+b") as f:
#     m = mmap.mmap(f.fileno(), 0, prot=mmap.PROT_READ)
#     while True:
#         line = m.readline()
#         if not line:
#             break
#         else:
#             w.write(line)
# w.close()
# print(time.time() - a)


a = time.time()
w = open('dd.json', 'w+b')
with open('dialog_extract_data.json', "r+b") as f:
    m = mmap.mmap(f.fileno(), 0, prot=mmap.PROT_READ)
    while True:
        line = m.read(1024*1024)
        if not line:
            break
        else:
            w.write(line)
w.close()
print(time.time() - a)


#实测read比readline要快，但是参数需要手动调整

```

综上，三种方法在同等机器配置，同等环境下，对900MB的文件处理速度分别约为：5.9s  ,  3.8s   , 0.9s
---
