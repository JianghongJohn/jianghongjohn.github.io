---
layout: post
title:  "组件化思想：cocoapod+coding搭建私人组件仓库"
date:   2017-05-30
categories: iOS
tags: iOS 编程思想
---

* content
{:toc}

### 一、首先在coding建立两个私人仓库（一个为组建远程索引仓库，一个为模板项目仓库）

![](https://github.com/JianghongJohn/jianghongjohn.github.io/blob/master/resource/iOS_pod_element_1.png?raw=true)
![](https://github.com/JianghongJohn/jianghongjohn.github.io/blob/master/resource/iOS_pod_element_2.png?raw=true)

### 二、在本地的cocoapod中添加我们自己的远程仓库(就是之前建的远程索引库地址)

```
~/.cocoapods/repos

pod repo add JnethRepo [https://git.coding.net/jianghongjohn/JnethSpec.git](https://git.coding.net/jianghongjohn/JnethSpec.git)

//这样就生成了你自己的本地仓库

```
![](https://github.com/JianghongJohn/jianghongjohn.github.io/blob/master/resource/iOS_pod_element_3.png?raw=trueg)

### 三、在任意的位置新建一个文件夹利用cocoapod建立repo模板

`pod lib create JHKit`

**生成的时候终端会有一些选项，按照提示输入然后回车就行**

这样就生成了一个模板，接下来就是将你的组件放入Classes文件中，并添加到工程中就可以了

![](https://github.com/JianghongJohn/jianghongjohn.github.io/blob/master/resource/iOS_pod_element_4.png?raw=true)
![](https://github.com/JianghongJohn/jianghongjohn.github.io/blob/master/resource/iOS_pod_element_5.png?raw=true)

### 四、修改podspec文件，并验证spec文件的有效性

![](https://github.com/JianghongJohn/jianghongjohn.github.io/blob/master/resource/iOS_pod_element_6.png?raw=true)

**期间要做的是验证spec文件的有效性**

输入命令 pod lib lint 

可能出现错误警告

```
[!] JHKit did not pass validation, due to 27 warnings (but you can use `--allow-warnings` to ignore them).
You can use the `--no-clean` option to inspect any issue.
```

将命令改成pod lib lint --allow-warnings

提示：JnethKit passed validation.

就是符合要求的spec文件了!

### 五、然后就可以将这一整个代码提交到远程仓库了（这时候并没有关联repo索引）

`git add .`

`git commit -m "first commit"`

`git remote add origin [https://git.coding.net/ZKReadStone/LoginModule.git](https://git.coding.net/ZKReadStone/LoginModule.git)`

`git push origin master`

给项目打标记

`git tag -m 'New Tag' 0.1.0`

`git push --tags`

这里默认是0.1.0，一定要与之前我们在Xcode中看到的s.version号一致！

![](https://github.com/JianghongJohn/jianghongjohn.github.io/blob/master/resource/iOS_pod_element_7.png?raw=true)

可以看到readme文件他也帮我们自动生成了

### 六、最后就是要给我们的远程索引仓库关联我们的本地索引库了，这样组件才会存到我们刚开始建的索引仓库中(*每次更新一次组件，都要push最新的上去*)

`pod repo push JnethRepo JHKit.podspec`

若出现警告依然可以使用--allow-warnings

`pod repo push JnethRepo JnethKit.podspec --allow-warnings`

![](https://github.com/JianghongJohn/jianghongjohn.github.io/blob/master/resource/iOS_pod_element_9.png?raw=true)


### 七、这样我们就可以正常使用cocoapod常规方式pod我们自己的组件了

注意在podfile中，我们必须添加source，否则会找不到资源

`source '[https://git.coding.net/jianghongjohn/JnethSpec.git](https://git.coding.net/jianghongjohn/JnethSpec.git)’`

![](https://github.com/JianghongJohn/jianghongjohn.github.io/blob/master/resource/iOS_pod_element_10.png?raw=true)

这样我们的组件库就成功通过cocoapod加入到我们的项目中了，而且所有关联他都自动加好了

![](https://github.com/JianghongJohn/jianghongjohn.github.io/blob/master/resource/iOS_pod_element_11.png?raw=true)

![](https://github.com/JianghongJohn/jianghongjohn.github.io/blob/master/resource/iOS_pod_element_12.png?raw=true)

`#import "JHKit-umbrella.h"`就可以使用所有的组件了

JHKit-umbrella.h 就是默认生成的我们引用库使用的header文件，可以更改为自己的header

### 补充

代码中使用[NSBundle mainBundle]加载资源资源文件会加载失败，因为文件已经不是这个mainBundle中了，用以下方法解决

修改podspec文件中的资源包，这样打包的时候他就会生成一个JnethKit.bundle资源包，Assets里面的文件就会打包到这个里面了

```
s.resource_bundles = {
     'JnethKit' => ['JnethKit/Assets/*']
   }
```

获取文件的方法

```
NSBundle *bundle = [NSBundle bundleForClass:[self class]];
NSString * filePath = [bundle pathForResource:@"address" ofType:@"txt" inDirectory:@"JnethKit.bundle"];
```