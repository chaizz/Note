---
title: flaskshell创建索引出错
author: chaizz
date: 2021-02-02 15:51:12
tags: Flask
categories: Flask
photos: ["https://tc.chaizz.com/a59930a4250a11ed94870242ac190002.png"]
---

​    

<!--more-->

遇到的问题：flask 环境中使用 flask shell  使用flask Whoosh 创建索引失败

### 解决办法 ：

    export FLASK_APP=manage.py
    flask shell 

然后导出 flask 创建的whoosh的whoosh对象
    
```
from 项目内的文件 import whoosh
whoosh.create_index()
```


​    

