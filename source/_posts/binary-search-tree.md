---
title: 二叉搜索树
date: 2016-08-07 11:36:57
tags: [每周总结, 数据结构]
---

二叉搜索树(BST)是二叉树的一种，但是它只允许在左侧结点存储比父结点小的值，在右侧结点存储比父结点大或等于的值。

<!-- more -->

## 二叉树的基本结构

术语：

- 深度：结点的深度取决于它的祖先结点的数量。
- 高度：树的高度取决于所有结点深度的最大值。
- 层：根结点在第 0 层，它的子结点在第 1 层，以此类推。

```javascript
// 结点类
class TreeNode {
  constructor(key) {
    this.key = key;
    this.left = null;
    this.right = null;
  }
}

// 二叉树
class Tree {
  constructor() {
    this.root = null;
  }
}
```

## 构建 BST

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/binary-search-tree/insert.gif)

上图直观地展示了一颗二叉搜索树构建的过程，每次插入结点时，从根结点开始比较结点间大小，然后将结点插入到正确的位置。

```javascript
class BinarySearchTree {
  constructor() {
    this.root = null;
  }

  insert(key) {
    let curNode = this.root;

    if(curNode === null) {
      this.root = new TreeNode(key);
      return;
    } else {
      while(curNode !== null) {
        if(key > curNode.key) {
          if(curNode.right === null) {
            curNode.right = new TreeNode(key);
            return;
          }
          curNode = curNode.right;
        } else if(key < curNode.key) {
          if(curNode.left === null) {
            curNode.left = new TreeNode(key);
            return;
          }
          curNode = curNode.left;
        }
      }
    }
  }
}
```

## 查找

### 树的遍历

访问树的结点有三种方法：中序、前序和后序。

- 中序遍历是指先访问结点的左孩子，然后访问结点自己，最后访问结点的右孩子。对每个孩子而言，规则也是一样的。
- 前序遍历是指先访问结点自己，然后访问结点的左孩子，最后访问结点的右孩子。
- 后序遍历是指先访问结点的左孩子，然后访问结点的右孩子，最后访问结点自己。

#### 中序遍历

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/binary-search-tree/inorder.png)

```javascript
inOrderTraverse(callback) {

  traverse(this.root);

  function traverse(node) {
    if(node == null) {
      return;
    }
    traverse(node.left);
    callback(node);
    traverse(node.right);
  }
}
```

#### 前序遍历

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/binary-search-tree/preorder.png)

```javascript
preOrderTraverse(callback) {

  traverse(this.root);

  function traverse(node) {
    if(node == null) {
      return;
    }
    callback(node);
    traverse(node.left);
    traverse(node.right);
  }
}
```

#### 后序遍历

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/binary-search-tree/postorder.png)

```javascript
postOrderTraverse(callback) {

  traverse(this.root);

  function traverse(node) {
    if(node == null) {
      return;
    }
    traverse(node.left);
    traverse(node.right);
    callback(node);
  }
}
```

### BST 的最小值和最大值

因为二叉搜索树会把比结点小的存在结点左边，把比结点大的存在结点右边，所以对二叉搜索树而言，树中的最小值在树的最左结点，树的最大值在树的最右结点。

#### 获取最小结点

```javascript
min(node) {
  let curNode = node || this.root;
  if(curNode !== null) {
    while(curNode && curNode.left !== null) {
      curNode = curNode.left;
    }
    return curNode;
  }
  
  return null;
}
```

#### 获取最大结点

```javascript
max(node) {
  let curNode = node || this.root;
  if(curNode !== null) {
    while(curNode && curNode.right !== null) {
      curNode = curNode.right;
    }
    return curNode;
  }
  
  return null;
}
```

### 搜素 BST 中的值

在二叉搜索树中进行搜索，和插入时一样进行比较即可。

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/binary-search-tree/search.gif)

```javascript
search(key) {
  let curNode = this.root;
  while(curNode !== null) {
    if(key < curNode.key) {
      curNode = curNode.left;
    } else if (key > curNode.key) {
      curNode = curNode.right;
    } else {
      return curNode;
    }
  }
  return null;
}
```

## 移除结点

移除结点包含了几种不同的运行场景，根据不同的情况，我们需要在移除后进行一些操作。

### 移除叶结点

移除叶结点不会对树产生影响，所以直接移除即可：

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/binary-search-tree/remove_0.png)

### 移除只有一个孩子的结点

因为该结点只有一个孩子，所以将该结点移除后，需要用孩子来填补该结点的位置：

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/binary-search-tree/remove_1.png)

### 移除有两个孩子的结点

这种情况相对以上两种要复杂一点，移除这个结点，需要执行三个步骤：

1. 找到该结点右子树的最小结点。
2. 用该结点右子树的最小结点的键更新该结点的键。
3. 移除该结点右子树的最小结点。

![](http://7xo08n.com1.z0.glb.clouddn.com/blog/binary-search-tree/remove_2.png)

### 实现
```javascript
remove(key) {

  let removeNode = (node, key) => {
    if(node == null) {
      return null;
    }

    if(key < node.key) {
      node.left = removeNode(node.left, key);
      return node;
    } else if(key > node.key) {
      node.right = removeNode(node.right, key);
      return node;
    } else {
      if(node.left == null && node.right == null) {
        node = null;
        return null;
      }

      if(node.left == null) {
        node = node.right;
        return node;
      }

      if(node.right == null) {
        node = node.left;
        return node;
      }

      // 找到右子树的最小结点
      let n = this.min(node.right);
      // 将该结点的值替换掉
      node.key = n.key;
      // 移除右子树的最小结点
      node.right = removeNode(node.right, n.key);

      return node;
    }
  }

  removeNode(this.root, key);
}
```