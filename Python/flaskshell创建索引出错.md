---
title: flaskshell创建索引出错
author: chaizz
date: 2021-02-02 15:51:12
tags: Flask、shell
---

遇到的问题：flask 环境中使用 flask shell  使用flask Whoosh 创建索引失败

<!--more-->

### 解决办法 ：

    export FLASK_APP=manage.py
    flask shell 

然后导出 flask 创建的whoosh的whoosh对象
    
```
from 项目内的文件 import whoosh
whoosh.create_index()
```


​    

