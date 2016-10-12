---
layout: post
title: 'Android Studio Version Management'
date: '2016-1-6'
description:
image: https://gradle.com/wp-content/uploads/2016/08/Insights.svg
image-sm: https://gradle.com/wp-content/uploads/2016/08/Insights.svg
categories:
  - Android
---

### Android Studio 版本自动更新

在开发Android项目的过程中，一些发布频率很高的应用，每次手动更改不是很方便，在一翻查找后，参考了这位同学的[博文](http://www.jianshu.com/p/e78cfc848d24)，感谢这位同学的无私奉献，我个人比较懒，为了考虑以后项目用的着，于是有些这篇文章。

#### 思路:
* 先加载项目的`gradle.properties`文件，一般生成项目这个文件都会存在
* 判断项目是否存在`VERSION_CODE`,到这里就清晰了，没有就生成一个
* 获取当前task的名称，仅当task包含`:app:assembleRelease`或`:app:generateReleaseSources`时，则判定为正式环境，更新`versionCode`
* 然后返回`versionCode`


#### 使用方法
>在`defaultConfig`中填写方法名

``` groovy
defaultConfig {
    applicationId "com.application"
    minSdkVersion 14
    targetSdkVersion 23
    versionCode updateVersion()
    versionName "1.0"
    multiDexEnabled true
  }
```

> 更新`versionCode`方法，放在`:app`的gradle文里面就行

``` groovy
def updateVersion() {
  def version
  def gradleFile = file('../gradle.properties')

  if (gradleFile.canRead()) {
    Properties properties = new Properties()
    properties.load(gradleFile.newInputStream())

    if (properties['VERSION_CODE'] == null) {
      properties['VERSION_CODE'] = '1'
      properties.store(gradleFile.newWriter(), null)
      properties.load(gradleFile.newInputStream())
    }

    version = properties['VERSION_CODE'] as int

    gradle.startParameter.taskNames.each { name ->
      println "${rootProject.name} taskName = ${name}"
      if (':app:assembleRelease'.equals(name) || ':app:generateReleaseSources'.equals(name)) {
        properties['VERSION_CODE'] = (++version) as String
        properties.store(gradleFile.newWriter(), "update version ${version}")
      }
    }
  } else {
    throw new GradleException("Could not read gradle.properties!")
  }
  return version
}
```
