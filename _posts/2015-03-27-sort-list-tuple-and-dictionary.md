---
layout: post
comments: true
title: "如何用 Python 内置的 sort() 函数排序"
description: ""
category:
tags: [python, sort]
---

## 概述

对序列的或是集合中的元素进行排序是很常见的问题。Python提供了内置的`sorted()`函数，以及为list对象提供的`list.sort()`方法。函数的格式及参数如下：

{% highlight python %}
sorted(iterable, cmp, key, reverse)
{% endhighlight %}

其中，*iterable* 是用于排序的集合，可以是list、tuple、dictionary也可以是其他的可以循环调用的对象。*cmp*, *key* 和 *reverse* 是三个可选参数。

- *cmp* 用于指定一个用户定义的比较函数，函数通过某种方式比较两个元素的值，根据比较的结果，在第一个元素*小于*、*等于*或*大于*第二个元素时，分别返回*负值*、*零*或*正值*。例如：`cmp=lambda x,y: cmp(x.lower(), y.lower())`。默认值为`None`。
- *key* 指定一个*函数*或一个*参数*，用于从集合的每一个元素中提取出用于比较的那个关键词，比如一个由tuple组成的list，在执行`sorted()`时，每次比较的元素将是一个tuple，这时就会涉及到需要按照tuple中的哪个元素比较的问题。又或者需要比较的元素是字符串，相同的字母大小写不同时会产生不同的排序，这时如果我们不关心大小写的差异，则可以指定比较小写字符串的值，例如： `key=str.lower`。默认的值是 `None`，也就是直接比较每个元素。
- *reverse* 需要输入一个布尔值。如果设置为 `True` 则排序的结果将变为逆序。

通常，通过指定 *key* 和 *reverse* 参数进行排序计算比 采用相应的 *cmp* 函数要快很多。

内置的 `sorted()` 函数能够保证排序结果的稳定。也就是说，可以保证具有相同值的元素的相对位置可以保持不变 —— 这点对具有多个项目的排序很有必要，例如优先按部门排序，对于相同部门再考虑工资等级来进行排序的情形。

## 基本应用

进行一个升序的排序非常简单，只要调用上述的 `sorted()` 函数，函数将会返回一个新的已经排序好的list：

{% highlight python %}
sorted([5, 2, 3, 1, 4]) # [1, 2, 3, 4, 5]
{% endhighlight %}

对于list对象还可以使用 `list.sort()` 方法。因为list是可变的（mutable），调用 `list.sort()` 方法，将直接改变list的值为排序后的结果，方法将会返回 `None` 作为返回值。通常还是建议使用 `sorted()` 函数，以避免犯错。

另外，`sorted()` 函数还可以用于其他可循环迭代的集合，如：

{% highlight python %}
sorted({1:'D',2:'B',3:'B',4:'E',5:'A'})
 # [1, 2, 3, 4, 5]
{% endhighlight %}

## Key 函数

从 Python 2.4 开始， `list.sort()` 方法和 `sorted()` 函数都增加了一个 `key` 参数，可以指定一个函数。如果指定了该参数，则在对每个元素做比较之前，都会先对参与比较的元素调用该函数。

例如，有一个字母大小写不敏感的排序问题：

{% highlight python %}
sorted("This is a test string from Andrew".split(), key=str.lower)
 # ['a', 'Andrew', 'from', 'is', 'string', 'test', 'This']
{% endhighlight %}

`key` 参数的输入值应该是函数名，该函数接受一个参数，并且返回一个用于排序的值。这个方法运算速度快，因为key所指定的函数对于每一个输入值仅调用一次。

比较通用的使用方法是用于对复合的对象进行排序，例如：

{% highlight python linenos %}
student_tuples = [
('john', 'A', 15),
('jane', 'B', 12),
('dave', 'B', 10),
]
sorted(student_tuples, key=lambda student: student[2])
 # sort by age
 # [('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
{% endhighlight %}

上面，用到了 `lambda` 表达式，同样的功能也可以用以下形式来实现：

{% highlight python %}
def getKet(item):
    return item[2]
sorted(student_tuples, key = getKey)
{% endhighlight %}

显然还是采用 `lambda` 更加简洁明了。

同样，该方法也可以应用于对象的属性，例如：

{% highlight python linenos %}
class Student:
    def __init__(self, name, grade, age):
        self.name = name
        self.grade = grade
        self.age = age
    def __repr__(self):
        return repr((self.name, self.grade, self.age))

student_objects = [
Student('john', 'A', 15),
Student('jane', 'B', 12),
Student('dave', 'B', 10),
]

sorted(student_objects, key=lambda student: student.age)
 # sort by age
 # [('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
{% endhighlight %}

## operator 模块提供的函数

上述的这种排序方式应用非常普遍，为此 Python 提供了专门的函数，用于更加方便快捷的完成上面的这种排序任务。在 `operator` 模块中，提供了 `itemgetter()` 和 `attrgetter()` 两个函数，用于获取特定位置的元素或是对象的某个属性。这样就免去了上面自己写函数或是 `lambda` 表达式的工作。使得排序的效率大大提升。

为了，使用这两个函数，需要将他们从 `operator` 模块中引用过来：

{% highlight python %}
from operator import itemgetter, attrgetter
{% endhighlight %}

用法如下：

{% highlight python %}
sorted(student_tuples, key=itemgetter(2))
 # [('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
sorted(student_objects, key=attrgetter('age'))
 # [('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
{% endhighlight %}

这两个函数的好处是，他们还可以支持 *多重排序* ，例如首先按成绩，再按年龄进行排序：

{% highlight python %}
sorted(student_tuples, key=itemgetter(1,2))
 # [('john', 'A', 15), ('dave', 'B', 10), ('jane', 'B', 12)]
sorted(student_objects, key=attrgetter('grade', 'age'))
 # [('john', 'A', 15), ('dave', 'B', 10), ('jane', 'B', 12)]
{% endhighlight %}

另外，Python 2.5 后的版本中， `operator` 模块还提供了 `operator.methodcaller()` 函数，该函数将会以固定的参数调用执行对象的方法。例如， 利用字符串对象的 `str.count()` 方法可以通过计算感叹号（!）的数量，来对信息按照重要程度来排序：

{% highlight python %}
messages = ['critical!!!', 'hurry!', 'standby', 'immediate!!']
sorted(messages, key=methodcaller('count', '!'))
 # ['standby', 'hurry!', 'immediate!!', 'critical!!!']
{% endhighlight %}

## 升序和降序

用 `list.sort()` 和 `sort()` 排序时生成的序列，默认是升序的。函数提供了一个可选参数 *reverse* ，该参数接受一个布尔值或布尔变量，当 `reverse = True` 时，将会按照降序排列。

## 排序的稳定性和复杂问题的排序

首先，简要的描述一下什么是排序的稳定性。当对某些多因素的问题进行排序时，有时我们只按照某一个因子进行排序，这时就可能出现多种不同的排序版本。这里，我们以扑克牌为例，假设有一个扑克的列表 `[黑桃7, 红桃5, 红桃2, 黑桃5]` ，这个列表中的每个元素包括 *数值* 和 *花色* 两个属性，如果此时我们只关注数值，按照数值的大小进行排序，那么对于上面的序列就可以生成以下两种排序结果：`[红桃2, 红桃5, 黑桃5, 黑桃7]` 和 `[红桃2, 黑桃5, 红桃5, 黑桃7]`。显然，在仅考虑数值排序的前提下，上述两个结果都是正确的。而在这个例子中，前一种排序结果我们称为稳定的，后面的则是不稳定的。所谓 *稳定性* 的意思就是说，排序算法将按照如下的规则进行：当遇到两个元素比较结果为相等时，比如上面例子中的黑桃5和红桃5；那么最终结果将保持其在原始序列中的相对位置，也就是说，如果在输入序列中一个元素在另一个之前，那么在输出序列中也将保持其在前，在上面扑克牌的例子中，输入序列里，红桃5在黑桃5之前，所以两个不同的输出序列中只有第一个是稳定的。

从 Python 2.2 开始，排序就都是稳定的。

{% highlight python %}
data = [('red', 1), ('blue', 1), ('red', 2), ('blue', 2)]
sorted(data, key=itemgetter(0))
 # [('blue', 1), ('blue', 2), ('red', 1), ('red', 2)]
{% endhighlight %}

这种排序稳定性的性质，为我们解决一些复杂的排序问题提供了便利。可以通过一系列排序步骤的组合来解决复杂排序问题。举个例子：接着上面的学生信息的问题，

{% highlight python linenos %}
student_tuples = [
('john', 'A', 15),
('jane', 'B', 12),
('dave', 'B', 10),
]

class Student:
    def __init__(self, name, grade, age):
        self.name = name
        self.grade = grade
        self.age = age
    def __repr__(self):
        return repr((self.name, self.grade, self.age))

student_objects = [
Student('john', 'A', 15),
Student('jane', 'B', 12),
Student('dave', 'B', 10),
]
{% endhighlight %}

每个学生有 *姓名*, *成绩* 和 *年龄* 三个信息，如果需要按照 *成绩* 的降序和 *年龄* 的升序来进行排序，那么我们可以首先对 *年龄* 按升序进行一次排序，然后再对 *成绩* 按照降序进行第二次排序。由于Python排序的稳定性，通过上述两个步骤就可以得到最终的结果，具体实施代码如下：

{% highlight python %}
s = sorted(student_tuples, key = itemgetter(2))     # sort on secondary key
sorted(s, key = itemgetter(1), reverse = True)      # now sort on primary key, descending

s = sorted(student_objects, key=attrgetter('age'))     # sort on secondary key
sorted(s, key=attrgetter('grade'), reverse=True)       # now sort on primary key, descending
{% endhighlight %}

---
2015-04
