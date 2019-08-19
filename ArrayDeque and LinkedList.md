# ArrayDeque and LinkedList



- **ArrayDeque和LinkedList都实现了Deque双端队，从两端取值/添加/删除.**
- **都可当作栈和队列使用，一个是数组实现一个是链表实现**

**ArrayDeque**,内部有3个成员，object数组，index 头，index尾

Object[] elements保存元素

int head,标记object数组的index 零

int tail标记object数组的index last=length-1

依靠两个下标索引Object数组实现队尾/对头的添加删除等.

**LinkedList，**内部有first节点和last节点    节点类型Node： 双向

```
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;

    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```



## LinkedList

##### **总体介绍**



![879896-20160312164228569-1798786054](C:\Users\Administrator\Desktop\MD文件\img\879896-20160312164228569-1798786054.jpg)

*LinkedList*同时实现了*List*接口和*Deque*接口，也就是说它既可以看作一个顺序容器，又可以看作一个队列（*Queue*），同时又可以看作一个栈（*Stack*）。这样看来，*LinkedList*简直就是个全能冠军。当你需要使用栈或者队列时，可以考虑使用*LinkedList*，一方面是因为Java官方已经声明不建议使用*Stack*类，更遗憾的是，Java里根本没有一个叫做*Queue*的类（它是个接口名字）。关于栈或队列，现在的首选是*ArrayDeque*，它有着比*LinkedList*（当作栈或队列使用时）有着更好的性能。![LinkedList_base](https://images2015.cnblogs.com/blog/939998/201605/939998-20160504081421341-1905413637.png)



*LinkedList*底层**通过双向链表实现**，本节将着重讲解插入和删除元素时双向链表的维护过程，也即是之间解跟*List*接口相关的函数，而将*Queue*和*Stack*以及*Deque*相关的知识放在下一节讲。双向链表的每个节点用内部类*Node*表示。*LinkedList*通过`first`和`last`引用分别指向链表的第一个和最后一个元素。注意这里没有所谓的哑元，当链表为空的时候`first`和`last`都指向`null`。



##### addLast



```
public void addLast(E e) {
    linkLast(e);
}
```

```
void linkLast(E e) {
    final Node<E> l = last;
    final Node<E> newNode = new Node<>(l, e, null);
    last = newNode;
    if (l == null)
        first = newNode;
    else
        l.next = newNode;
    size++;
    modCount++;
}
```

普通双向链表尾插法。  首位不相连

##### add

```
public boolean add(E e) {
    linkLast(e);
    return true;
}
```

一样的操作 区别有个返回值true

##### addFirst

```
public void addFirst(E e) {
    linkFirst(e);
}
```

```
private void linkFirst(E e) {
    final Node<E> f = first;
    final Node<E> newNode = new Node<>(null, e, f);
    first = newNode;
    if (f == null)
        last = newNode;
    else
        f.prev = newNode;
    size++;
    modCount++;
}
```

头插法

##### add(int index, E element)

```java
   public void add(int index, E element) {
        checkPositionIndex(index);

        if (index == size)
            linkLast(element);
        else
            linkBefore(element, node(index));
    }
```

```
private boolean isPositionIndex(int index) {
    return index >= 0 && index <= size;
}
```

```
Node<E> node(int index) {
    // assert isElementIndex(index);
			//size》》1 是size的一半   在左边就first指针找 快一些
    if (index < (size >> 1)) {
        Node<E> x = first;
        for (int i = 0; i < index; i++)
            x = x.next;
        return x;
    } else {			//右边就last指针找
        Node<E> x = last;
        for (int i = size - 1; i > index; i--)
            x = x.prev;
        return x;
    }
}
```

```
//succ是index位置元素  把插入元素链接到succ pre 的nex    然后插入元素next链接succ																	
void linkBefore(E e, Node<E> succ) {
    // assert succ != null;
    final Node<E> pred = succ.prev;
    final Node<E> newNode = new Node<>(pred, e, succ);
    succ.prev = newNode;
    if (pred == null)
        first = newNode;
    else
        pred.next = newNode;
    size++;
    modCount++;
}

```

 **LinkedList当作栈使用** 



![微信截图_20190814161634](C:\Users\Administrator\Desktop\MD文件\img\微信截图_20190814161634.png)



​	**当作队列使用**



![微信截图_20190814162614](C:\Users\Administrator\Desktop\MD文件\img\微信截图_20190814162614.png)

​		



![微信截图_20190816171446](C:\Users\Administrator\Desktop\MD文件\img\微信截图_20190816171446.png)

**由于LinkedList继承了Deque所以可以当作栈和队列使用**



## ArrayDeque

Java里有一个叫做*Stack*的类，却没有叫做*Queue*的类（它是个接口名字）。当需要使用栈时，Java已不推荐使用*Stack*，而是推荐使用更高效的*ArrayDeque*；既然*Queue*只是一个接口，当需要使用队列时也就首选*ArrayDeque*了（次选是*LinkedList*）。

##### 总体介绍

要讲栈和队列，首先要讲*Deque*接口。*Deque*的含义是“double ended queue”，即双端队列，它既可以当作栈使用，也可以当作队列使用。下表列出了*Deque*与*Queue*相对应的接口：

| Queue Method | Equivalent Deque Method | 说明                                   |
| ------------ | ----------------------- | -------------------------------------- |
| `add(e)`     | `addLast(e)`            | 向队尾插入元素，失败则抛出异常         |
| `offer(e)`   | `offerLast(e)`          | 向队尾插入元素，失败则返回`false`      |
| `remove()`   | `removeFirst()`         | 获取并删除队首元素，失败则抛出异常     |
| `poll()`     | `pollFirst()`           | 获取并删除队首元素，失败则返回`null`   |
| `element()`  | `getFirst()`            | 获取但不删除队首元素，失败则抛出异常   |
| `peek()`     | `peekFirst()`           | 获取但不删除队首元素，失败则返回`null` |

上面两个表共定义了*Deque*的12个方法。添加，删除，取值都有两个方法，它们功能相同，区别是对失败情况的处理不同。**一套接口遇到失败就会抛出异常，另一套遇到失败会返回特殊值（false或null）**。除非某种实现对容量有限制，大多数情况下，添加操作是不会失败的。



从名字可以看出*ArrayDeque*底层通过数组实现，为了满足可以同时在数组两端插入或删除元素的需求，该数组还必须是循环的，即**循环数组（circular array）**，也就是说数组的任何一点都可能被看作起点或者终点。*ArrayDeque*是非线程安全的（not thread-safe），当多个线程同时使用的时候，需要程序员手动同步；另外，该容器不允许放入`null`元素。![ArrayDeque_base.png](https://images2015.cnblogs.com/blog/939998/201605/939998-20160507172937875-1444735637.png)

上图中我们看到，**head指向首端第一个有效元素，tail指向尾端第一个可以插入元素的空位**。因为是循环数组，所以`head`不一定总等于0，`tail`也不一定总是比`head`大。

##### add函数

```
public void addFirst(E e) {
    if (e == null)
        throw new NullPointerException();
    elements[head = (head - 1) & (elements.length - 1)] = e;
    if (head == tail)
        doubleCapacity();
}
```

