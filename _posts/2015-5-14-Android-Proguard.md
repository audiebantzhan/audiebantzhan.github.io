---
layout: post
title: 'Android Proguard'
date: '2015-5-14'
description:
image: https://unsplash.it/2000/1200?image=1003
image-sm: https://unsplash.it/500/300?image=1003
categories:
  - Android
---

### 语法
> `-include {filename} `   从给定的文件中读取配置参数
>
> `-basedirectory {directoryname} `   指定基础目录为以后相对的档案名称
>
> `-injars {class_path} `   指定要处理的应用程序jar,war,ear和目录
>
> `-outjars {class_path}`    指定处理完后要输出的jar,war,ear和目录的名称
>
> `-libraryjars {classpath} `   指定要处理的应用程序jar,war,ear和目录所需要的程序库文件
>
> `-dontskipnonpubliclibraryclasses`    指定不去忽略非公共的库类。
>
> `-dontskipnonpubliclibraryclassmembers `   指定不去忽略包可见的库类的成员。

* 保留选项

> `-keep {Modifier} {class_specification} `   保护指定的类文件和类的成员
>
> `-keepclassmembers {modifier} {class_specification}`    保护指定类的成员，如果此类受到保护他们会保护的更好
>
> `-keepclasseswithmembers {class_specification}`    保护指定的类和类的成员，但条件是所有指定的类和类成员是要存在。
>
> `-keepnames {class_specification}  `  保护指定的类和类的成员的名称（如果他们不会压缩步骤中删除）
>
> `-keepclassmembernames {class_specification}`    保护指定的类的成员的名称（如果他们不会压缩步骤中删除）
>
> `-keepclasseswithmembernames {class_specification}`    保护指定的类和类的成员的名称，如果所有指定的类成员出席（在压缩步骤之后）
>
> `-printseeds {filename}`   列出类和类的成员-keep选项的清单，标准输出到给定的文件

* 压缩

> `-dontshrink  `  不压缩输入的类文件
>
> `-printusage {filename}`
>
> `-whyareyoukeeping {class_specification}`

* 优化

> `-dontoptimize  `  不优化输入的类文件
>
> `-assumenosideeffects {class_specification} `   优化时假设指定的方法，没有任何副作用
>
> `-allowaccessmodification  ` 优化时允许访问并修改有修饰符的类和类的成员

* 混淆

> `-dontobfuscate`    不混淆输入的类文件
>
>` -printmapping {filename}`
>
> `-applymapping {filename}`    重用映射增加混淆
>
> `-obfuscationdictionary {filename} `   使用给定文件中的关键字作为要混淆方法的名称
>
> `-overloadaggressively `   混淆时应用侵入式重载
>
> `-useuniqueclassmembernames `   确定统一的混淆类的成员名称来增加混淆
>
> `-flattenpackagehierarchy {package_name} `   重新包装所有重命名的包并放在给定的单一包中
>
>`-repackageclass {package_name}`   重新包装所有重命名的类文件中放在给定的单一包中
>
> `-dontusemixedcaseclassnames `   混淆时不会产生形形色色的类名
>
> `-keepattributes {attribute_name,...} `   保护给定的可选属性，例如LineNumberTable, LocalVariableTable, SourceFile, Deprecated, Synthetic, Signature, and

* InnerClasses.

> `-renamesourcefileattribute {string} `   设置源文件中给定的字符串常量
