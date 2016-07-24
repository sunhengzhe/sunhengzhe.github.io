---
title: 基本数据结构
date: 2016-07-24 13:39:57
tags: [每周总结, 数据结构]
---

通过 Javascript 实现各种基本数据结构。包括栈、队列、链表、集合等

<!-- more -->

## 栈

栈算是相对来说最简单的数据结构了，遵循后进先出原则，可以通过维护一个数组来实现一个栈类。

### API
- push(ele1, [ele2, ...]) 添加一个或多个元素到栈顶
- pop() 弹出栈顶元素
- peek() 返回栈顶元素，但不弹出它
- isEmpty() 如果栈为空，则返回 true，否则返回 false
- clear() 清空栈
- size() 返回栈中元素个数

### 实现
```javascript
class Stack {
  constructor() {
    this.arr = [];
  }

  /**
   * 出栈
   * @return 栈顶元素
   */
  pop() {
    if(this.isEmpty()) {
      throw new Error('stack underflow error');
    }
    return this.arr.pop();
  }

  /**
   * 入栈
   * @params  新元素
   */
  push() {
    const args = Array.from(arguments);
    if(args.length == 0) {
      throw new Error('缺少参数');
    }
    for(let ele of args) {
      this.arr.push(ele);
    }
  }

  /**
   * 返回栈顶元素但不出栈
   * @return 栈顶元素
   */
  peek() {
    if(this.isEmpty) {
      throw new Error('该栈为空栈');
    }
    return this.arr[this.arr.length - 1];
  }

  /**
   * 是否为空栈
   * @return boolean 是否为空栈
   */
  isEmpty() {
    return this.size() == 0;
  }

  /**
   * 栈的大小
   * @return number 栈的大小
   */
  size() {
    return this.arr.length;
  }

  /**
   * 清空栈
   */
  clear() {
    this.arr = [];
  }

  /**
   * 打印栈中元素
   * @return string 栈中所有元素
   */
  print() {
    console.log(this.arr.toString());
  }
}
```

### 应用

#### 十进制转其他进制

我们可以使用栈来解决进制转换的问题，从十进制转指定的进制实际上是让十进制数除以进制的基数，再将得到的结果继续作被除数，一直到商为 0 为止。将每次的余数压栈，最后让栈依次弹出，连接成的字符串即为转换后的数。

```javascript
function baseConverter(decNumber, base) {
  const stack = new Stack();
  const digits = '0123456789ABCDEF';
  let result = '';

  while(decNumber > 0) {
    let rem = Math.floor(decNumber % base);
    stack.push(digits[rem]);
    decNumber = Math.floor(decNumber / base);
  }

  while(!stack.isEmpty()) {
    result += stack.pop();
  }

  return result;
}

console.log(baseConverter(100345, 2)); // 11000011111111001
console.log(baseConverter(100345, 8));  // 303771
console.log(baseConverter(100345, 16)); // 187F9
```

## 队列

队列和栈很类似，但遵循先进先出原则。

### API
- enqueue(ele1, [ele2, ...]) 向队尾添加一个或多个元素
- dequeue() 移除队首元素，并返回
- front() 返回队首元素，但不移除它
- isEmpty() 队列为空时，返回 true，否则返回 false
- size() 返回队列中元素个数
- clear() 清空队列
- print() 打印队列

### 实现
```javascript
class Queue {
  constructor() {
    this.arr = [];
  }

  enqueue() {
    const args = Array.from(arguments);
    if(args.length == 0) {
      throw new Error('缺少参数');
    }
    for(let ele of args) {
      this.arr.push(ele);
    }
  }

  dequeue() {
    if(this.isEmpty()) {
      throw new Error('queue is empty');
    }
    return this.arr.shift();
  }

  front() {
    if(this.isEmpty()) {
      throw new Error('queue is empty');
    }
    return this.arr[0];
  }

  isEmpty() {
    return this.size() == 0;
  }

  size() {
    return this.arr.length;
  }

  clear() {
    this.arr = [];
  }

  print() {
    console.log(this.arr.toString());
  }
}
```

### 应用

#### 优先队列

优先队列中的每个元素都有一个优先级，优先级的数字越小，优先级越高。为了使优先级越高的元素越先出队列，添加该元素的时候，会根据它的优先级将它插入到合适的位置。

```javascript
class PriorityQueue extends Queue{
  constructor(...args) {
    super(...args);
  }

  enqueue(element, priority) {
    const ele = {
      element: element,
      priority: priority
    };

    if(this.isEmpty()) {
      this.arr.push(ele);
      return;
    }

    let added = false;
    for(let i in this.arr) {
      if(priority < this.arr[i].priority) {
        this.arr.splice(i, 0, ele);
        added = true;
        break;
      }
    }

    if(!added) {
      this.arr.push(ele);
    }
  }

  print() {
    console.log(this.arr.map((item) => {
      return item.element;
    }).join(','));
  }
}

let priorityQueue = new PriorityQueue();
priorityQueue.enqueue('john', 2);
priorityQueue.enqueue('jack', 1);
priorityQueue.enqueue('mark', 3);
priorityQueue.enqueue('camila', 1); 
priorityQueue.print();  // jack,camila,john,mark
```

#### 循环队列——击鼓传花

假设有一圈小孩围着坐，将一朵花交给队首的小孩，小孩依次将花传给下一个小孩，当传到队尾的时候，再将花传给队首的小孩，也就是队列首尾相连，形成了一个循环队列。

在“击鼓传花”游戏中，给定一个数字，当传递次数达到这个次数时，淘汰掉此时拿花的小孩，再进行下一轮，直到只有一个小孩（胜者）。

```javascript
function hotPotato(nameList, num) {
  const queue = new Queue();

  // 小孩入列
  for (let name of nameList) {
    queue.enqueue(name);
  }

  while(queue.size() > 1) {
    for(let i = 0; i < num; i++) {
      // 将队首的小孩放到队尾
      queue.enqueue(queue.dequeue());
    }
    // 淘汰
    console.log(queue.dequeue() + ' 被淘汰了。');
  }

  return queue.dequeue();
}

let nameList = ['John', 'Jack', 'Camila', 'Ingrid', 'Carl'];
let winner = hotPotato(nameList, 7);
/**
 * Camila 被淘汰了。
 * Jack 被淘汰了。
 * Carl 被淘汰了。
 * Ingrid 被淘汰了。
 * winner: John
 */
console.log('winner: ' + winner);
```

## 链表

链表存储有序的元素集合，但不同于数组，链表中的元素在内存中不是连续放置的，每个元素由一个存储元素本身的节点和一个指向下一个元素的引用组成。

### API
- append(element) 在尾部添加一个新元素
- insert(position, element) 在指定位置插入一个新元素
- remove(element) 从链表中移除一个元素
- indexOf(element) 返回元素在链表中的索引，如果没有返回 -1
- removeAt(position) 从特定位置移除一个元素
- isEmpty() 返回链表是否为空
- size() 链表中元素的个数
- toString() 输出所有元素的值

### 实现
```javascript
/* 结点类 */
class Node {
  constructor(element, next = null) {
    this.element = element;
    this.next = next;
  }
}

/* 链表类 */
class LinkedList {
  constructor() {
    // 链表头
    this.head = null;
    // 链表长度
    this.length = 0;
  }

  append(element) {
    const node = new Node(element);

    if(this.head == null) {
      this.head = node;
    } else {
      let current = this.head;
      while(current.next != null) {
        current = current.next;
      }
      current.next = node;      
    }

    this.length++;
  }

  insert(position, element) {
    if(position < 0 || position >= this.size()) {
      throw new Error('illegal position');
    }
    const node = new Node(element);
    let current = this.head, previous = null;
    if(position == 0) {
      node.next = this.head;
      this.head = node;
    } else {
      let index = 0;
      while(index < position) {
        previous = current;
        current = current.next;
        index++;
      }
      node.next = current;
      previous.next = node;
    }
    this.length++;
  }

  removeAt(position) {
    if(this.isEmpty()) {
      throw new Error('list is empty');
    }
    if(position < 0 || position >= this.size()) {
      throw new Error('illegal position');
    }
    let current = this.head, previous = null;
    if(position == 0) {
      this.head = current.next;
    } else {
      let index = 0;
      while(index < position) {
        previous = current;
        current = current.next;
        index++;
      }

      previous.next = current.next;
    }
    this.length--;
    return current.element;
  }

  remove(element) {
    const index = this.indexOf(element);
    this.removeAt(index);
  }

  indexOf(element) {
    let current = this.head;
    let index = -1;
    while(current) {
      index++;
      if(current.element == element) {
        return index;
      }
      current = current.next;
    }
    return -1;
  }

  isEmpty() {
    return this.size() == 0;
  }

  size() {
    return this.length;
  }

  toString() {
    let result = '';
    let current = this.head;
    while(current) {
      result += current.element;
      current = current.next;
    }
    return result;
  }

  print() {

  }
}
```

## 集合

集合由一组无序且唯一的项组成。

### API
- add(value) 向集合添加一个元素
- remove(value) 从集合内移除一个元素 
- has(value) 元素是否在集合中
- clear() 清空集合
- size() 返回集合所包含的元素数量
- values() 返回包含集合中所有值的数组

### 实现
```javascript
class Set {
  constructor() {
    this.items = {};
  }

  has(value) {
    return value in this.items;
  }

  add(value) {
    if(!this.has(value)) {
      this.items[value] = value;
      return true;
    }
    return false;
  }

  remove(value) {
    if(this.has(value)) {
      delete this.items[value];
      return true;
    }
    return false;
  }

  clear() {
    this.items = {};
  }

  size() {
    return Object.keys(this.items).length;
  }

  values() {
    return Object.keys(this.items);
  }
}
```

### 集合操作
```javascript
class Set {
  constructor() {
    this.items = {};
  }

  //...

  /**
   * 并集
   */
  union(otherSet) {
    const union = new Set();

    for(let item in this.items) {
      union.add(item);
    }

    let values = otherSet.values();
    for(let value of values) {
      console.log(value);
      union.add(value);
    }

    return union;
  }

  /**
   * 交集
   */
  intersection(otherSet) {
    const intersection = new Set();
    for(let item in this.items) {
      if(otherSet.has(item)) {
        intersection.add(item);
      }
    }

    return intersection;
  }

  /**
   * 差集
   */
  difference(otherSet) {
    const difference = new Set();

    for(let item in this.items) {
      if(!otherSet.has(item)) {
        difference.add(item);
      }
    }

    return difference;
  }

  /**
   * 当前集合是否为给定集合的子集
   */
  subset(otherSet) {
    if(this.size() > otherSet.size()) {
      return false;
    }
    for(let item in this.items) {
      if(!otherSet.has(item)) {
        return false;
      }
    }
    return true;
  }
}
```