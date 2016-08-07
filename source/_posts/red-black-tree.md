---
title: 红黑树
date: 2016-08-07 23:10:57
tags: [每周总结, 数据结构]
---

红黑树是二叉搜索树的一种，同时也是一种平衡搜索树，在红黑树中，没有一条路径会比其他路径长出 2 倍，因此它是近似平衡的。

<!-- more -->

红黑树中的每个结点都有一个颜色，颜色可以是黑色或者红色，红黑树根据结点的颜色对树进行约束，从而保持树的平衡。

一颗红黑树是满足下面红黑性质的二叉搜索树：

1. 每个结点要么是红色，要么是黑色
2. 根结点是黑色的
3. 每个叶结点是黑色的
4. 如果一个结点是红色，则它的两个子结点都是黑色的
5. 对每个结点，从该结点到其所有后代叶结点的简单路径上，均包含相同数目的黑色结点

由性质 2 和 4 可知，红黑树中最短的路径是结点全部为黑色的路径，最长的路径是红黑相间的路径，所以在红黑树中，不会出现一条路径比其他路径长 2 倍。

相比于以前使用 null 来表示边界，在红黑树中，我们引入哨兵 `nil` 来处理边界条件。哨兵和普通结点拥有相同属性，但作为叶子结点的哨兵根据性质 3，它的颜色应该是黑色的。

如下就是一颗红黑树：

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/red-black-tree/red-black-tree.png)

## 旋转

红黑树和二叉搜索树很类似，但因为引入了红黑性质，所以在对红黑树进行插入和删除等操作的时候，很可能破坏红黑性质，这时候需要对树进行调整，改变一些结点的位置。

旋转是调整树结构的一种操作，分为左旋和右旋。如下

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/red-black-tree/rotate.png)

在旋转中，只有指针会改变，其他属性都保持不变。

```javascript
leftRotate(node) {
  let rNode = node.right;
  node.right = rNode.left;
  if(rNode.left != this.nil) {
    rNode.left.parent = node;
  }

  rNode.parent = node.parent;
  rNode.left = node;
  node.parent = rNode;

  if(rNode.parent == this.nil) {
    this.root = rNode;
  } else {
    if(rNode.key < rNode.parent.key) {
      rNode.parent.left = rNode;
    } else {
      rNode.parent.right = rNode;
    }
  }
}

rightRotate(node) {
  let lNode = node.left;
  node.left = lNode.right;
  if(lNode.right != this.nil) {
    lNode.right.parent = node;
  }

  lNode.parent = node.parent;
  lNode.right = node;
  node.parent = lNode;

  if(lNode.parent == this.nil) {
    this.root = lNode;
  } else {
    if(lNode.key < lNode.parent.key) {
      lNode.parent.left = lNode;
    } else {
      lNode.parent.right = lNode;
    }
  }
}
```

## 插入

将新结点插入到红黑树中时，如果新结点是黑色，则势必会破坏性质 5，而红色可能一条性质也不会破坏，所以我们选择红色为新结点的默认颜色。

插入之前我们需要先找到要插入的位置，这里和二叉搜索树是一样的。

```javascript
class TreeNode {
  constructor() {
    this.parent = null;
    this.left = null;
    this.right = null;
    this.key = 'nil';
    this.color = 'black';
  }

  toString() {
    return this.key + ',' + this.color;
  }
}

class RedBlackTree {

  constructor() {
    this.nil = new TreeNode();
    this.root = this.nil;
  }

  insert(key) {
    let curNode = this.root;
    let parent = this.nil;
    // 找到要插入的位置
    while(curNode != this.nil) {
      parent = curNode;
      if(key < curNode.key) {
        curNode = curNode.left;
      } else {
        curNode = curNode.right;
      }
    }

    let node = new TreeNode();
    node.key = key;
    node.parent = parent;
    node.left = this.nil;
    node.right = this.nil;

    if(parent == this.nil) {
      // 如果树中没有结点，插入结点为根结点
      node.color = 'black';
      this.root = node;
    } else {
      // 插入到正确的地方
      node.color = 'red';
      if(node.key < parent.key) {
        parent.left = node;
      } else {
        parent.right = node;
      }
      // 调整树的结构
      this.insertFix(node);
    }
  }
}
```

`insertFix` 方法的作用是在插入结点后调整树的结构，在有新的红色结点插入到树中后，有哪些性质可能被破坏呢？

- 性质 1 显然不会被破坏。

- 如果插入元素是根结点时，会破坏性质 2。

- 性质 3 不会被破坏，因为叶子结点始终是哨兵。

- 如果插入元素的父结点是红色，那么性质 4 会被破坏。

- 性质 5 不会被破坏，因为插入的结点是红色，不会影响路径中黑色结点的个数。

所以在调整树的结构时，我们只需关注性质 2 和 4。

### 情况一

如果插入结点的父结点是黑色结点，那么性质 12345 全部继续成立，不需要调整树的结构。

### 情况二

如果插入结点的父结点是红色结点。

因为插入之前这课树是一颗红黑树，那么插入结点的父结点的父结点（图中的 B 结点）必定是黑色（否则违反性质 4）。如下图，R 表示红色，B 表示黑色：

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/red-black-tree/01.png)

但不确定 C 是什么颜色。

#### a) C 是红色

因为 D 和 B 的颜色起了冲突，必定需要改变其中一个结点的颜色，如果改变 D 的颜色，会破坏性质 5，而如果改变 B 的颜色，为了维持性质 5，我们可以选择将 A 和 C 的颜色也改变，即：

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/red-black-tree/02.png)

这个时候性质 5 不会被破坏，但因为 A 的颜色变了，可能会与 A 的父结点颜色冲突，所以我们需要把 A 看作新插入结点，重新进行判断。

#### b) C 是黑色或者 C 是 nil

如果 C 是黑色，我们仍然改变 A 和 B 的颜色，但是为了保证性质 5 成立，所以可以对 A 和 B 进行右旋：

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/red-black-tree/03.png)

当 D 是 B 的右子树时，先将 B 和 D 左旋，然后变成上面这种情况，再对 A、B 进行右旋。

以上情况二是假设 D 和 B 是 A 的左子树，当 D 和 B 是 A 的右子树时，需要把方向换一下，原理是相同的。

```Javascript
class RedBlackTree {

  constructor() {
    //...
  }

  insert(key) {
    //...
  }

  insertFix(node) {
    while(node.parent.color == 'red') {
      if(node.parent == node.parent.parent.left) {
        let uncle = node.parent.parent.right;
        if(uncle.color == 'red') {
          // 叔结点是红色
          node.parent.color = 'black';
          uncle.color = 'black';
          node.parent.parent.color = 'red';
          node = node.parent.parent;
        } else {
          // 旋转
          if(node == node.parent.right) {
            node = node.parent;
            this.leftRotate(node);
          }
          node.parent.color = 'black';
          node.parent.parent.color = 'red';
          this.rightRotate(node.parent.parent); 
        }
      } else {
        let uncle = node.parent.parent.left;
        if(uncle.color == 'red') {
          // 叔结点是红色
          node.parent.color = 'black';
          uncle.color = 'black';
          node.parent.parent.color = 'red';
          node = node.parent.parent;
        } else {
          if(node == node.parent.left) {
            node = node.parent;
            this.rightRotate(node);
          }
          node.parent.color = 'black';
          node.parent.parent.color = 'red';
          this.leftRotate(node.parent.parent);
        }
      }
    }
    this.root.color = 'black';
  }

  leftRotate(node) {
    //...
  }

  rightRotate(node) {
    //...
  }
}
```