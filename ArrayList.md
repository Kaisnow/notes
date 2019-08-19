# ArrayList

#### 总体介绍

​          ArrayList*实现了*List*接口，是顺序容器，即元素存放的数据与放进去的顺序相同，允许放入`null`元素，底层通过**数组实现**。除该类未实现同步外，其余跟*Vector*大致相同。每个*ArrayList*都有一个容量（capacity），表示底层数组的实际大小，容器内存储元素的个数不能多于当前容量。当向容器中添加元素时，如果容量不足，容器会自动增大底层数组的大小。这里的数组是一个Object数组，以便能够容纳任何类型的对象。![ArrayList_base](https://images2015.cnblogs.com/blog/939998/201604/939998-20160422082704601-766043722.png)



size(), isEmpty(), get(), set()方法均能在常数时间内完成，add()方法的时间开销跟插入位置有关，addAll()方法的时间开销跟添加元素的个数成正比。其余方法大都是线性时间。

为追求效率，ArrayList没有实现同步（synchronized），如果需要多个线程并发访问，用户可以手动同步，也可使用Vector替代。

#### Arraylist初始化

```
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
```



```
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
```

这是java1.8版本的初始化 初始化一个空数组 以前版本应该是初始化大小为10的数组.

#### add函数

```
 public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }
```

**进入 ensureCapacityInternal(size + 1);** 


private void ensureCapacityInternal(int minCapacity) {
          //注意括号里还有一个函数
      ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}


**继续进入ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));**

**和中间的calculateCapacity(elementData, minCapacity)**，（眼神不好就看掉了hhh）

minCapacity传进来的是size+1

```
private static int calculateCapacity(Object[] elementData, int minCapacity) {
  
 		 初始化时elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA
   if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
  		   会返回默认容量10或者size+1   中大的一个，实际意思就是当前需要的容量大小
        return Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    return minCapacity;
}
```

```
private static final int DEFAULT_CAPACITY = 10;
```



```
private void ensureExplicitCapacity(int minCapacity) {

    moCount是修改次数 这个是用iterater遍历时防止修改数据的  这个不详细说明
    modCount++;

    // overflow-conscious code
    如果返回的容量大小大于现在数组大小就扩容，意思就是数组装不下了或者数组大小不到10，我们直接给他grow10.    
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}
```

所以虽然1.8初始化大小为0，但是添加元素就会让他 grow10 或者添加元素总大小

毕竟可能第一下就addAll添加100个 ，那就要扩容100及以上啦



```
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);//原来的1.5倍
    if (newCapacity - minCapacity < 0)
    		如果刚才传进来的值大于 原数组大小的1.5倍
        newCapacity = minCapacity;
        	这个一般用不到 就是数组很大很大的时候
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
  			扩容到newCapacity大小并复制
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

反正搞这么多东西只需要明白他就是为了得到一个合适的空间大小扩容

而扩容就是用的Arrays.copyOf函数

- 如果第一次添加7个以内元素直接扩容到10个（7*1.5=10，8的话就会扩容12个了）
- **注意第一次扩容后elementData不指向DEFAULTCAPACITY_EMPTY_ELEMENTDATA数组了。指向了新new的数组**
- **指向了新数组后 上面calculateCapacity函数的判断就不会比较10和size+1的大小了，直接返回size+1**





#### addAll（）

 ~~~java
public boolean addAll(Collection<? extends E> c) {
        Object[] a = c.toArray();
        int numNew = a.length;
        ensureCapacityInternal(size + numNew);  // Increments modCount
        System.arraycopy(a, 0, elementData, size, numNew);
        size += numNew;
        return numNew != 0;
    }
 ~~~

没啥好说的和上面差不多主要是扩容差别就是这里是   size+numNew





#### remove()

~~~java
public E remove(int index) {
    rangeCheck(index);
    modCount++;
    E oldValue = elementData(index);
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index, numMoved);
    elementData[--size] = null; //清除该位置的引用，让GC起作用
    return oldValue;
}
~~~

