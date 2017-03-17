---
title: 基本数据结构（二）
date: 2016-07-26 23:39:57
tags: [数据结构]
---

通过 Javascript 实现各种基本数据结构。包括字典、散列表、图等。

<!-- more -->

## 字典

字典存储一个键值对，相当于封装了对对象的操作，比较简单，不展开说明。

### API
- keys() 以数组形式返回字典包含的所有键名
- has(key) 返回字典是否包含键值
- set(key, value) 存储一个键值对
- remove(key) 根据键值删除对应的键值对
- get(key) 返回指定键名对应的值
- values() 以数组形式返回字典内所有值
- size() 返回字典内键值对的数量

### 实现

```javascript
class Dictionary {
  constructor() {
    this.items = {};
  }

  keys() {
    return Object.keys(this.items);
  }

  has(key) {
    return key in this.items;
  }

  set(key, value) {
    this.items[key] = value;
  }

  remove(key) {
    if(this.has(key)) {
      delete this.items[key];
      return true;
    }
    return false;
  }

  get(key) {
    return this.has(key) ? this.items[key] : undefined;
  }

  values() {
    let result = [];
    for(let key in this.items) {
      if(this.has(key)) {
        result.push(this.items[key]);
      }
    }
    return result;
  }

  size() {
    return this.keys().length;
  }
}
```

## 散列表

散列表是普通数组的推广，它是实现字典操作的一种有效数据结构。

### API
- put(key, value) 向散列表添加一个项
- remove(key) 根据键值从散列表中移除值
- get(key) 返回根据键值检索到的特定的值

### 散列函数

将数据插入到散列表的时候，需要通过散列函数来计算出这个数据存放的位置，这样每次取数据的时候，只需要再次通过散列函数计算便能知道数据的位置了。

一个表现良好的散列函数是由几个方面构成的：插入和检索元素的时间（即性能），还包括较低的冲突可能性。比如如下散列函数：

```javascript
function djb2HashCode(key) {
  let hash = 5381;
  for(let i = 0; i < key.length; i++) {
    hash = hash * 33 + key.charCodeAt(i);
  }
  return hash % 1013;
}
```

当 key 为 `Gandalf` 时，该函数返回 `798`，代表该数据将存在数组下标为 798 的位置。

显然，`put`、`remove` 和 `get` 方法都是围绕这个值来工作的。

```javascript
class HashTable {
  constructor() {
    this.arr = [];
  }

  put(key, value) {
    const position = djb2HashCode(key);
    this.arr[position] = value;
  }

  remove(key) {
    const position = djb2HashCode(key);
    this.arr[position] = undefined;
  }

  get(key) {
    const position = djb2HashCode(key);
    return this.arr[position];
  }
}
```

### 解决冲突

显然，这种方式存在一个致命的弱点：当两个 `key` 值计算出来的哈希值是相同的时候，两个元素将被插入同一个地方，后插入的元素将覆盖掉先插入的元素，这称为发生了冲突。

处理冲突有几种方法：分离链接、线性探查和双散列法。

#### 1. 分离链接

分离链接在散列表的每个位置创建一个链表，存在相同位置的元素时，元素将被添加到链表的末尾。

```javascript
class ValuePair {
  constructor(key, value) {
    this.key = key;
    this.value = value;
  }

  toString() {
    return this.key + ',' + this.value;
  }
}

class HashTable {
  constructor() {
    this.arr = [];
  }

  put(key, value) {
    const position = djb2HashCode(key);
    let list = this.arr[position];
    if(this.arr[position] == undefined) {
      this.arr[position] = new LinkedList();
    }
    this.arr[position].append(new ValuePair(key, value));
  }

  remove(key) {
    const position = djb2HashCode(key);
    const list = this.arr[position];

    if(list != undefined) {
      let current = list.getHead();

      while(current.next) {
        if(current.element.key == key) {
          list.remove(current.element);
          if(list.isEmpty()) {
            this.arr[position] = undefined;
          }
          return true;
        }
        current = current.next;
      }

      if(current.element.key == key) {
        list.remove(current.element);
        if(list.isEmpty()) {
          this.arr[position] = undefined;
        }
        return true;
      }
    }
    return false;
  }

  get(key) {
    const position = djb2HashCode(key);
    const list = this.arr[position];

    if(list != undefined) {
      let current = list.getHead();
      while(current.next) {
        if(current.element.key == key) {
          return current.element.value;
        }
        current = current.next;
      }

      if(current.element.key == key) {
        return current.element.value;
      }
    }
    return undefined;
  }
}
```

#### 2. 线性探查

线性探查指当插入新元素时，如果索引为 `index` 的位置已经被占据了，就尝试 `index + 1` 的位置，如果 `index + 1` 的位置也被占据了，就尝试 `index + 2` 的位置，以此类推。

```javascript
class HashTable {
  constructor() {
    this.arr = [];
  }

  put(key, value) {
    const position = djb2HashCode(key);

    if(this.arr[position] == undefined) {
      this.arr[position] = new ValuePair(key, value);
    } else {
      let index = position + 1;
      while(this.arr[index] != undefined) {
        index++;
      }
      this.arr[index] = new ValuePair(key, value);
    }
  }

  get(key) {
    const position = djb2HashCode(key);

    if(this.arr[position] != undefined) {
      let index = position;
      while(this.arr[index] != undefined) {
        if(this.arr[index].key == key) {
          return this.arr[index].value;
        }
        index++;
      }
    }

    return undefined;
  }

  remove(key) {
    const position = djb2HashCode(key);

    if(this.arr[position] != undefined) {
      let index = position;
      while(this.arr[index] != undefined) {
        if(this.arr[index].key == key) {
          this.arr[index] = undefined;
          // 重新调整散列表
          for(let i = index; i < this.arr.length; i++) {
            this.arr[i] = this.arr[i + 1];
          }
          this.arr.length--;
          return true;
        }
        index++;
      }
    }

    return false;
  }
}
```

使用线性探查需要注意的是：
- 在一些语言中，需要定义数组的大小，当为占据的数据寻找下一个可用位置的时候，数组的可用位置可能会被用完，这就需要根据一些策略改变散列表的大小。但是 Javascript 里面不用担心这个问题，因为数组长度会根据需要自动改变大小。
- 删除一个
数据的时候，需要重新调整这个位置以及之后的数据，因为删除会使这个位置为空，造成之后查询这个位置时被认为没有这个数据。

## 图

### 基本概念
- 相邻顶点：由一条边连接在一起的顶点
- 度：一个顶点的度是其相邻顶点的数量
- 路径：路径是顶点 v1, v2, ..., vk 的一个连续序列，其中 vi 和 v(i+1) 是相邻的
- 简单路径：简单路径是不包含重复顶点的路径

### 图的表示

一个图 G=(V, E) 由以下元素组成：

- V: 一组顶点
- E: 一组边，连接 V 中的顶点

![图](http://7xo08n.com1.z0.glb.clouddn.com/blog/data-structor-2/01.png)

#### 邻接矩阵

在矩阵中，索引为 i 的结点和索引为 j 的结点相邻，则 `arr[i][j] === 1`，否则 `arr[i][j] !== 1`。

![邻接矩阵](http://7xo08n.com1.z0.glb.clouddn.com/blog/data-structor-2/02.png)

在无向图中，矩阵是一个对称矩阵。

#### 邻接表

邻接表由图中每个顶点和每个顶点的相邻顶点列表组成。

![邻接表](http://7xo08n.com1.z0.glb.clouddn.com/blog/data-structor-2/03.png)

#### 关联矩阵

关联矩阵中，矩阵的行表示顶点，列表示边。

![关联矩阵](http://7xo08n.com1.z0.glb.clouddn.com/blog/data-structor-2/04.png)

### 实现

以邻接表描述图实现如下：

```javascript
class Graph {
  constructor() {
    // 顶点集合
    this.vertics = [];
    // 邻接表
    this.adjList = new Dictionary();
  }

  addVertex(v) {
    this.vertics.push(v);
    this.adjList.set(v, []);
  }

  addEdge(v, w) {
    this.adjList.get(v).push(w);
    this.adjList.get(w).push(v);
  }

  toString() {
    let result = '';
    for(let vertex of this.vertics) {
      result += vertex + ' =>';
      let neighbors = this.adjList.get(vertex);
      for(let neighbor of neighbors) {
        result += ' ' + neighbor;
      }
      result += '\n';
    }
    return result;
  }
}
```

### 图的遍历

图的遍历算法的思想是必须追踪每一个第一次访问的结点，并且追踪有哪些结点还没有被完全探索。对于图的两种遍历算法，都需要明确指定第一个被访问的结点。

我们用三种颜色来标注顶点，用来表示它们的状态：

- 白色：表示该顶点还没有被访问过
- 灰色：表示该顶点被访问过，但没有被探索过
- 黑色：表示该顶点被访问且已经被探索

#### 广度优先搜索(Breadth-First Search, BFS)

![广度优先搜索](http://7xo08n.com1.z0.glb.clouddn.com/blog/data-structor-2/05.png)

广度优先搜素先宽后深访问顶点，从顶点 v 开始的 BFS 算法步骤如下：

1. 创建一个队列 Q
2. 将 v 标注为灰色，并将 v 入队列
3. 如果 Q 非空，则运行以下步骤：
   a) 队首 u 出队列
   b) 将 u 标记为灰色
   c) 将 u 所有白色相邻顶点入队列
   d) 将 u 标注为黑色

```javascript
class Graph {
  //...

  initColor() {
    const colors = [];
    for(let vertex of this.vertics) {
      colors[vertex] = 'white';
    }
    return colors;
  }

  bfs(v, callback) {
    const colors = this.initColor();
    const queue = new Queue();

    queue.enqueue(v);
    colors[v] = 'gray';

    while(!queue.isEmpty()) {
      let curVertex = queue.dequeue();
      let neighbors = this.adjList.get(curVertex);
      // 遍历当前结点的相邻结点
      for(let neighbor of neighbors) {
        // 将未访问的结点插入队列并标记为未探索
        if(colors[neighbor] == 'white') {
          queue.enqueue(neighbor);
          colors[neighbor] = 'gray';
        }
      }
      // 当前结点已经探索完
      colors[curVertex] = 'black';
      callback && callback(curVertex);
    }
  }
}
```


#### 深度优先搜索(Depth-First Search, DFS)

![深度优先搜索](http://7xo08n.com1.z0.glb.clouddn.com/blog/data-structor-2/06.png)

广度优先搜素先深后宽访问顶点，从顶点 v 开始的 DFS 算法步骤如下：

1. 将 v 标注为灰色
2. 对 v 的所有白色相邻顶点 w ：
   a) 访问 w
3. 将 v 标记为黑色

```javascript
class Graph {
  //...

  initColor() {
    const colors = [];
    for(let vertex of this.vertics) {
      colors[vertex] = 'white';
    }
    return colors;
  }

  dfs(v, callback) {
    const colors = this.initColor();

    var dfsVisit = (v) => {
      // 访问当前结点
      colors[v] = 'gray';

      callback && callback(v);

      let neighbors = this.adjList.get(v);
      // 探索当前结点
      for(let neighbor of neighbors) {
        if(colors[neighbor] == 'white') {
          dfsVisit(neighbor);
        }
      }
      colors[v] = 'black';
    }

    dfsVisit(v);
  }
}
```

#### 结果

```javascript
var graph = new Graph();
var vertics = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I'];
for(let vertex of vertics) {
  graph.addVertex(vertex);
}

graph.addEdge('A', 'B');
graph.addEdge('A', 'C');
graph.addEdge('A', 'D');
graph.addEdge('C', 'D');
graph.addEdge('C', 'G');
graph.addEdge('D', 'G');
graph.addEdge('D', 'H');
graph.addEdge('B', 'E');
graph.addEdge('B', 'F');
graph.addEdge('E', 'I');

/**
 * 广度优先
 * A
 * B
 * C
 * D
 * E
 * F
 * G
 * H
 * I
 */
graph.bfs('A', v => console.log(v))

/**
 * 深度优先
 * A
 * B
 * E
 * I
 * F
 * C
 * D
 * H
 * G
 */
graph.dfs('A', v => console.log(v))
```