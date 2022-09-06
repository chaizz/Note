---
title: Python压缩图片作为缩略图
author: chaizz
date: 2021-2-2 15:54:36
tags: Python压缩图片
categories: Python
photos: ["https://tc.chaizz.com/1c078eb22dca11ed9d170242ac190002.jpg"]
---

​           

<!--more-->

将图片压缩，作为缩略图，代码如下

```python
# coding=utf-8
from PIL import Image
import shutil
```

```python
import os
from PIL import ImageFile
ImageFile.LOAD_TRUNCATED_IMAGES = True    #   OSError: image file is truncated   图像被截断错误

def resize_by_size(infile):
    """按照生成图片文件大小进行处理(单位KB)"""
    file_name = infile.split('/')[-1]
    if file_name.split('.')[-1] not in ['jpg','JPG','PNG','png']:
        pass
    else:
        outfilename = 'thumbnail' + infile.split('/')[-1]
        outfile = '/'.join(infile.split('/')[:-1]) + '/' + outfilename
        im = Image.open(infile)
        size_tmp = os.path.getsize(infile)
        q = 100
        while size_tmp > 10240 and q > 0:
            out = im.resize(im.size, Image.ANTIALIAS)
            out.save(outfile, quality=q)
            size_tmp = os.path.getsize(outfile)
            q -= 10
        if q == 100:
            shutil.copy(infile,outfile)


def equal_img(infile):
    """
    修改大小
    :param cls:
    :return:
    """


    file_name = infile.split('/')[-1]
    if file_name.startswith('thumbnail') or file_name.startswith('.'):
        pass
    else:
        # 判断是不是图片。不是的话，忽略
        if file_name.split('.')[-1] not in ['jpg','JPG','PNG','png','jpeg','JPEG']:
            pass
        else:
            outfilename = 'thumbnail' + infile.split('/')[-1]
            # print('file_name：',outfilename)
            outfile = '/'.join(infile.split('/')[:-1]) + '/' + outfilename
            # print(u'path：',outfile)
            im = Image.open(infile)
            width = im.size[0]   # 获取宽度
            height = im.size[1]   # 获取高度
            print(u'width：height：',width,height)
            if width <= 200:
                shutil.copy(infile,outfile)
            else:
                coe = float('%.2f'%(200/width))   #保留两位小数可能是0.00，
                print(u'coe：',coe)
                if coe <= 0:
                    coe = 0.15
                if im.mode == "P":
                    im = im.convert('RGB')
                out = im.resize((int(width*coe), int(height*coe)), Image.ANTIALIAS)

                out.save(outfile)





def gci(filepath):
    #遍历filepath下所有文件，包括子目录
    files = os.listdir(filepath)
    for fi in files:
        fi_d = os.path.join(filepath,fi)
        if os.path.isdir(fi_d):
            gci(fi_d)
        else:
            # resize_by_size(os.path.join(filepath,fi_d))
            equal_img(os.path.join(filepath,fi_d))

#递归遍历/root目录下所有文件
# gci('/var/local/www/edu_online1/media/works')
# gci('/root/www/edu_online1/media/works/13360067818/tradition')  #正式
gci('/root/www/edu_online1/media/works/13265647342/udict/1552525240000/䤋')  #正式
# gci(r'F:\desktop\䤋')
```

