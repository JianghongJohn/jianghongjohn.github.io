---
layout: post
title:  "Python一键处理iOS马甲包的文件更改"
date:   2018-12-27 20:43
categories: python
tags: iOS python
---

* content
{:toc}

###一、修改文件前缀(JH-->Jneth)
```
import os

需要修改的类名前缀 （需替换）
pre_str = 'JH'
// 新的类名前缀 （需替换）
pre_to_str = 'Jneth'
// 搜寻以下文件类型 （根据自己需求替换）
suf_set = ('.h', '.m', '.xib', '.storyboard', '.mm')
// 项目路径   （找到自己的项目路径）
project_path = '/Users/hyjt/Desktop/car/car'
// 项目project.pbxproj文件路径 需要更新配置文件中的类名 （找到自己的项目project.pbxproj路径）
pbxpro_path = '/Users/hyjt/Desktop/car/car.xcodeproj/project.pbxproj'

// 文件重命名函数，返回新的文件名
def file_rename(file_path):
    root_path = os.path.split(file_path)[0]     // 文件目录
    root_name = os.path.split(file_path)[1]     // 文件名包含扩展名
    filename = os.path.splitext(root_name)[0];  // 文件名
    filetype = os.path.splitext(root_name)[1];  // 文件扩展名

    new_path = os.path.join(root_path, filename.replace(pre_str, pre_to_str) + filetype)    // 拼接新路径
    os.renames(file_path, new_path)             // 文件重命名
    return filename.replace(pre_str, pre_to_str)

// 定义一个字典 key=旧类名 value=新类名
needModifyDic = {}

// 遍历文件，符合规则的进行重命名
for (root, dirs, files) in os.walk(project_path):
    for file_name in files:
        if file_name.startswith((pre_str,)) and file_name.endswith(suf_set):
            old_name = os.path.splitext(file_name)[0]
            new_name = file_rename(os.path.join(root, file_name))
            needModifyDic[old_name] = new_name

// 遍历文件，在文件中更换新类名的引用
print(needModifyDic)
for (root, dirs, files) in os.walk(project_path):
    for file_name in files:
        if file_name.endswith(suf_set):
            print('-----fileName-------' + file_name)
            with open(os.path.join(root, file_name), 'r+') as f:
                print('========fileName========' + file_name)
                s0 = f.read()
                f.close()
                for key in needModifyDic:
                    if key in s0:
                        with open(os.path.join(root, file_name), 'r+') as f4:
                            s1 = f4.read().replace(key, needModifyDic[key])
                            print(key + ' ------> ' + needModifyDic[key])
                            f4.seek(0)
                            f4.write(s1)
                            f4.truncate()
                            f4.close()
// 替换配置文件中的类名
for key in needModifyDic:
    with open(pbxpro_path, 'r+') as f:
        s0 = f.read()
        f.close()
        if key in s0:
            with open(pbxpro_path, 'r+') as f2:
                s = f2.read().replace(key, needModifyDic[key])
                f2.seek(0)
                f2.write(s)
                f2.truncate()
                f2.close()
```

###二、修改文件后缀（ViewModel-->VM）
```
import os

// 需要修改的类名前缀 （需替换）
end_str = 'ViewModel'
// 不需要修改的类名后缀 （需替换）
not_end_str = []
// 新的类名后缀 （需替换）
end_to_str = 'VM'



// 搜寻以下文件类型 （根据自己需求替换）
suf_set = ('.h', '.m', '.xib', '.storyboard', '.mm')
// 项目路径   （找到自己的项目路径）
project_path = '/Users/hyjt/Desktop/car/car'
// 项目project.pbxproj文件路径 需要更新配置文件中的类名 （找到自己的项目project.pbxproj路径）
pbxpro_path = '/Users/hyjt/Desktop/car/car.xcodeproj/project.pbxproj'

// 文件重命名函数，返回新的文件名
def file_rename(file_path):
    root_path = os.path.split(file_path)[0]     // 文件目录
    root_name = os.path.split(file_path)[1]     // 文件名包含扩展名
    filename = os.path.splitext(root_name)[0];  // 文件名
    filetype = os.path.splitext(root_name)[1];  // 文件扩展名

    new_path = os.path.join(root_path, filename.replace(end_str, end_to_str) + filetype)    // 拼接新路径
    // print(new_path)
    os.renames(file_path, new_path)             // 文件重命名
    return filename.replace(end_str, end_to_str)
def end_check(name):
    for str in not_end_str:
        if name.find(str) != -1 or name == end_str:
            return True
    return False
// 定义一个字典 key=旧类名 value=新类名
needModifyDic = {}

// 遍历文件，符合规则的进行重命名
for (root, dirs, files) in os.walk(project_path):
    for file_name in files:
        fileName = os.path.splitext(file_name)[0];  // 文件名
        if fileName.endswith(end_str) and file_name.endswith(suf_set) and end_check(fileName)==False:
            
            // print(file_name)
            old_name = os.path.splitext(file_name)[0]
            new_name = file_rename(os.path.join(root, file_name))
            needModifyDic[old_name] = new_name

// 遍历文件，在文件中更换新类名的引用
print(needModifyDic)
for (root, dirs, files) in os.walk(project_path):
    for file_name in files:
        if file_name.endswith(suf_set):
            print('-----fileName-------' + file_name)
            with open(os.path.join(root, file_name), 'r+') as f:
                print('========fileName========' + file_name)
                s0 = f.read()
                f.close()
                for key in needModifyDic:
                    if key in s0:
                        with open(os.path.join(root, file_name), 'r+') as f4:
                            s1 = f4.read().replace(key, needModifyDic[key])
                            print(key + ' ------> ' + needModifyDic[key])
                            f4.seek(0)
                            f4.write(s1)
                            f4.truncate()
                            f4.close()
// 替换配置文件中的类名
for key in needModifyDic:
    with open(pbxpro_path, 'r+') as f:
        s0 = f.read()
        f.close()
        if key in s0:
            with open(pbxpro_path, 'r+') as f2:
                s = f2.read().replace(key, needModifyDic[key])
                f2.seek(0)
                f2.write(s)
                f2.truncate()
                f2.close()

```
###三、修改资源文件MD5值，不改变图片(jpg,png)

```

import hashlib
import os
// 获取MD5
def GetFileMd5(filename):
    if not os.path.isfile(filename):
        return
    myhash = hashlib.md5()
    f = open(filename,'rb')
    while True:
        b = f.read(8096)
        if not b :
            break
        myhash.update(b)
    f.close()
    return myhash.hexdigest()
// 给文件添加末尾，改变md5
def fileAppend(filename):
    myfile = open(filename,'a')
    // 添加一个自定义内容，并不影响文件
    myfile.write("jneth")
    myfile.close
// 设置以这些结尾的
suf_set = ('.png', '.jpg')
project_path = '/Users/hyjt/Desktop/起源车贷/qiyuan-ios/car/car/'
// 遍历文件，符合规则的进行重命名
// 项目路径   （找到自己的项目路径）


for (root, dirs, files) in os.walk(project_path):
    for file_name in files:
        if file_name.endswith(suf_set):
            short_name = os.path.splitext(file_name)[0]
            realpath = os.path.join(root, file_name)
            print(short_name + ' ==> ' + realpath)
            oldMd5 = GetFileMd5(realpath)
            fileAppend(realpath)
            newMd5 = GetFileMd5(realpath)
            print(oldMd5 + '-->' + newMd5)


```