---
layout: post
title: '简易代码重构对应表'
date: '2015-6-4'
description:
categories:
image: https://tctechcrunch2011.files.wordpress.com/2015/04/codecode.jpg?w=1288
image-sm: https://tctechcrunch2011.files.wordpress.com/2015/04/codecode.jpg?w=738
categories:
  - 笔记
---

### 简易重构代码对应表

<table>
<thead>
<tr>
<th>问题描述</th>
<th>一般重构方法</th>
<th>模式重构</th>
</tr>
</thead>
<tbody>
<tr>
<td>重复代码</td>
<td>提炼方法<br> 取类<br> 方法上移<br> 替换算法<br> 链构造方法</td>
<td>构造Template Method<br> 以Composite取代一/多之分<br> 引入Null Object<br> 用Adapter统一接口<br>用Fatory Method引入多态创建</td>
</tr>
<tr>
<td>过长方法</td>
<td>提取方法 <br>组合方法<br>以查询取代临时变量<br> 引入参数对象 <br>保持对象完整</td>
<td>转移聚集操作到Vistor <br> 以Strategy取代条件逻辑 <br>以Command取代条件调度程序 <br>转移聚集操作到Collecting Parameter</td>
</tr>
<tr>
<td>过长参数列</td>
<td>以方法取代参数<br> 引入参数对象 <br>保持对象完整</td>
<td></td>
</tr>
<tr>
<td>条件逻辑过度复杂</td>
<td>分解条件式<br> 合并条件式<br> 合并重复的条件片段<br> 移除控制标记 <br>以卫语句取代嵌套条件式 <br>以多态取代条件式 <br>引入断言</td>
<td>以Strategy取代条件逻辑<br>转移装饰功能到Decorator<br>以State取代状态改变条件语句<br>引入Null Object</td>
</tr>
<tr>
<td>分支语句</td>
<td>提取方法 <br>转移方法<br> 以子类取代类型代码<br> 以多态取代条件式 <br>已明确方法取代参数</td>
<td>以State/Strategy取代类型代码<br>引入Null Object<br>以Command替换条件调度程序<br>转移聚集操作到Visitor</td>
</tr>
<tr>
<td>基本类型迷恋<br>程序代码过于依赖基本类型<br>(int，string，double，array等低层次语言要素)</td>
<td>以对象取代数据值<br>以类型取代类型代码<br>以子类取代类型代码<br>提取类<br>引入参数对象<br>以对象取代数组<br></td>
<td>以State取代状态改变条件语句<br>以Strategy取代条件逻辑<br>以Composite取代隐含树<br>以Interpreter取代隐式语言<br>转移装饰功能到Decorator<br>用Builder封装Composite<br></td>
</tr>
<tr>
<td>数据泥团<br>在类的字段和参数列中，总是一起出现的数据</td>
<td>提取类<br>引入参数对象<br>保持对象完整</td>
<td></td>
</tr>
<tr>
<td>令人迷惑的临时字段</td>
<td>提取类</td>
<td>引入Null Object</td>
</tr>
<tr>
<td>组合爆炸<br>许多段代码使用不同种类或数量的数据或对象做同样的事情<br>(例如使用特定条件和数据库查询)</td>
<td>以Interpreter取代隐式语言</td>
</tr>
<tr>
<td>过大类</td>
<td>提取类<br>提取子类<br>提取接口<br>复制被监视数据</td>
<td>以Command取代条件调度程序<br>以State取代状态改变条件语句<br>以Interpreter取代隐式语言</td>
</tr>
<tr>
<td>冗余类<br>不再做很多工作或没有用的类</td>
<td>折叠继承关系<br>内联Singleton</td>
<td></td>
</tr>
<tr>
<td>不恰当的暴露<br>在客户代码中不应看到类的字段和方法却是公开可见的</td>
<td>封装字段<br>封装群集移除设置方法<br>隐藏方法</td>
<td>用Factory封装类</td>
</tr>
<tr>
<td>发散式变化<br>类经常因为不同的原因在不同方向上发生变化，显然是违反了单一职责原则</td>
<td>提取类</td>
<td></td>
</tr>
<tr>
<td>霰弹式修改<br>如果遇到变化，必须在不同的类中作出相应的修改</td>
<td>转移方法<br>转移字段<br>内联类</td>
<td>将创建知识搬移到Factory</td>
</tr>
<tr>
<td>依恋情结<br>方法对于某个类的兴趣高过对自己所处的宿主类</td>
<td>转移方法<br>提取方法</td>
<td>引入Strategy<br>引入Visitor</td>
</tr>
<tr>
<td>平行继承体系<br>当为一个类增加一个子类时，也必须在另一个类中增加一个相应的子类</td>
<td>转移方法<br>转移字段</td>
<td></td>
</tr>
<tr>
<td>夸夸其谈未来性</td>
<td>折叠继承关系<br>内联类<br>移除参数<br>移除方法</td>
<td></td>
</tr>
<tr>
<td>过度耦合的消息连<br>不断的向一个对象索求另一个对象</td>
<td>隐藏委托<br>提取方法<br>转移方法</td>
<td>使用抽象引入Chain Of Responsibility</td>
</tr>
<tr>
<td>中间转手人<br>类接口中有很多方法都委托给其他类</td>
<td>移除中间转手人<br>内联方法<br>以继承取代委托</td>
<td></td>
</tr>
<tr>
<td>狎昵关系<br>类之间彼此依赖于其private成员</td>
<td>转移方法<br>将双向关联改为单向<br>提取类<br>隐藏委托<br>以继承取代委托</td>
<td></td>
</tr>
<tr>
<td>异曲同工的类</td>
<td>重命名方法<br>转移方法<br>提取超类</td>
<td>用Adapter统一接口</td>
</tr>
<tr>
<td>不完善的程序库类</td>
<td>引入外加方法<br>引入本地扩展</td>
<td>用Adapter统一接口<br>用Facade封装类</td>
</tr>
<tr>
<td>纯稚的数据类<br>只拥有字段的数据类</td>
<td>封装字段<br>封装集合<br>移除设置方法<br>转移方法<br>隐藏方法</td>
<td></td>
</tr>
<tr>
<td>被拒绝的遗赠<br>继承父类时，子类想要选择继承的成员</td>
<td>以委托取代继承</td>
<td></td>
</tr>
<tr>
<td>过多的注释<br>为糟糕的代码写大量的注释</td>
<td>使用一起重构方法，使方法本身达到自说明的效果，让注释显得多余</td>
<td></td>
</tr>
<tr>
<td>怪异解决方案<br>在同一系统中使用不同的方式解决同一问题</td>
<td>替换算法</td>
<td>用Adapter统一接口</td>
</tr>
</tbody>
</table>


参考:
[坏代码的味道](http://www.cnblogs.com/mywolrd/archive/2012/04/24/2467395.html)
