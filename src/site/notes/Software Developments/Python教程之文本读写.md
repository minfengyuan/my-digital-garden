---
{"dg-publish":true,"title":"Python教程之文本读写","created":"2024-01-16 15:19","updated":"2024-01-29 15:00","tags":["python"],"permalink":"/Software Developments/Python教程之文本读写/","dgPassFrontmatter":true,"noteIcon":"1"}
---


本文包含对 TXT、JSON 等格式文件如何读写的记录。

# TXT

- `read()`: 一次性读取文本中全部的内容，以字符串的形式返回结果

	```python
	with open("test.txt", "r") as f:# 打开文件
	    data = f.read()# 读取文件
	    print(data)
	```

- `readline()`: 只读取文本第一行的内容，以字符串的形式返回结果

	```python
	with open("test.txt", "r") as f:
	    data = f.readline()
	    print(data)
	```

- `readlines()`: 读取文本所有内容，并且以数列的格式返回结果，一般配合 for in 使用

	```python
	with open("test.txt", "r") as f:
	    data = f.readlines()
	    print(data)
	```

	Notes: readlines 会读到换行符，可用如下方法去除

	```python
	with open("test.txt", "r") as f:
	    for line in f.readlines():
	        line = line.strip('\n')#去掉列表中每一个元素的换行符
	        print(line)
	```

- `write()`:

- `writelines()`:

# JSON

- `json.load()`: 读取文件，返回 python 对象
- `json.dump()`: 写入文件，文件为 json 字符串格式，无返回
- `json.loads()`: 将 json 字符串转换为字典类型，返回 python 对象
- `json.dumps()`: 将 python 中的字典类型转换为字符串类型，返回 json 字符串

> [!note]  
> `load` 和 `dump` 处理的主要是文件  
> `loads` 和 `dumps` 处理的是字符串  
> `json.dumps(ensure_ascii=False)` 可以输出中文