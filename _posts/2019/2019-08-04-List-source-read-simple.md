---
layout: post
title:  List源码的简单解读
no-post-nav: true
category: other
tags: [java 源码]
excerpt: java list 源码
---
# List源码的简单解读
## ADD操作

 1. 疯狂想象  
步骤 | 可能的问题
---|---
 add之前判断是否有足够的空间|空间判断规则
不够，要进行增加| 如何扩增加
够，添加|添加之后是否需要进行剩余空间判断
2. 问题的口嗨
- 首先，对于空间肯定要判断要不空间不够肯定加不进去啊。就想我们生活中在往箱子塞东西，满肯定就放不进去了啊。
- 空间的判断，就用大小啊。就像盒子的空间大小52`${m^2}$`。看看有没有足够的剩余空间。
3. 源码简单看

**add中有以下2个方法**：  

```
# 确认空间大小
ensureCapacityInternal(size + 1);
# 将新增的对象放到合适的空间位置上。
 elementData[size++] = e
```
- **ensureCapacityInternal**方法，只有一个方法。
```
 ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
 ```
- calculateCapacity(elementData, minCapacity)     
```
if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        return Math.max(DEFAULT_CAPACITY, minCapacity);
    }
 return minCapacity;
 ```
该方法用于计算数组的容量。意思也很容易理解，DEFAULT_CAPACITY是默认初始容量为10。
minCapacity是指当前数据的需要的最小容量。

举个例子，当我们使用默认的构造函数new 一个list对象。此时，我们的DEFAULT_CAPACITY=10 （默认值 ）我们可以再源码中找到一下代码:
```
  private static final int DEFAULT_CAPACITY = 10;
 ```
 DEFAULTCAPACITY_EMPTY_ELEMENTDATA:
 ```
  private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
 ````
 我们可以看到它是空数组。
 那么此时
 ```
 if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) 
```
条件满足,方法返回DEFAULT_CAPACITY和minCapacity中的较大值。此次返回minCapacity=10。
不满足返回minCapacity。
- **ensureExplicitCapacity(int minCapacity)**
```
private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
```
该方法作用：
1. 用modCount实现，修改数据的个数记录
2. 判断是否需要扩容，判断条件如下：
```
 if (minCapacity - elementData.length > 0)
```
上面我们的例子中，返回的minCapacity=10，elementData为空数组，所以条件满足。
3. 条用扩容方法grow(minCapacity)
- grow(minCapacity)
```
private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```
扩容方法中包括以下几个步骤：
1. 先将原来数组的大小安装1.5倍的系数进行扩大，设置为新容量。
2. 确认newCapacity是否比需要的最小容量小，如果要小，就将minCapacity赋值给newCapacity。
3. 接着对newCapacity进行判断是否大于List的最大容量。
4. 使用newCapacity最为新数组的大小，对原数组进行复制。

## ROMOVE

 1. 疯狂想想象     
步骤 | 可能的问题
---|---
判断index是否在数组范围内 |可能就是根据index是否在数组长度-1内。
移除后将数组重新构造|位置改变，应该需要对原来进行重新构造(怎么处理)

2. 问题的口嗨
- 根据我们使用的过程，我们应该都遇到过ArarryIndexOutOfBound错误，所以应该是根据数据下标进行判断。
- 对于remove后的数组，应该是将原数组从remove的index+1,依次向前移动。
3. 源码简单看
- romve方法
```
 public E remove(int index) {
        rangeCheck(index);

        modCount++;
        E oldValue = elementData(index);

        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work

        return oldValue;
    }
```
它有3个主要方法：

**rangeCheck(index)**
```
private void rangeCheck(int index) {
        if (index >= size)
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
```
从名字就能知道，它是用来检测index是否在0数组size-1内。实现也很简单。

**E oldValue = elementData(index)**    
根据index从原数据中获取数据，用于反回。

**重构数据** 乱取的名字 2333333
```
int numMoved = size - index - 1;
        if (numMoved > 0)
System.arraycopy(elementData, index+1, elementData, index
```
首先，判断了是否要进行移动。根据的条件也很简单，通过index和size来得到移动的元素数量。举个例子：我们remove的index=0，原数组的length为10,那么我们要移动的数量为11。即要从第二个开始依次向前移动。

**GC回收资源**
```
  elementData[--size] = null; // clear to let GC do its work
```
这段代码的意义是将移动后的数据的最后一个元素置空，以便GC去回收资源。

<b style="color:red">可能中间会有诸多错误，但是收获也是从发现错误开始，来源于解决错误的过期，汲取自对错误的总结。</b>
## 心灵自白
冷静，在焦躁的日子里多些耐心；渴望，在平淡的日子多些追求；思考，让我可以多些选择。-致现在的我。
