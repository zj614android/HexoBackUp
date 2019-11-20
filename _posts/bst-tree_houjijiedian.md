------
layout: pages
title: bst-tree_后继节点
date: 2019年05月06日16:06:51
tags: 树
categories: 数据结构/算法
------

## 1.前言

[前一篇我们对BST树做了一个入门，](https://blog.csdn.net/user11223344abc/article/details/84556070)这一篇我们来对BST树找后继节点做个学习，为什么要学这个，因为后面再做删除节点的时候会用到这个知识点。废话不多说，直接开始。

前驱后继节点针对的是树的三种遍历模式下各个节点在不同排列顺序下的位置关系。比如树A在先序遍历下的排列顺序是{3 5 8 7 6}，3是5的前驱节点，8是5的后继节点。

但是说前中后序三种遍历方式都有各自的前驱后继节点找法。

本文只讨论以**BST树以中序遍历找后继节点**为例当做一个入门，更多相关的知识请自行google。

另外本文如有错误请直接指出，感谢。
## 2.如何寻找后继节点？
### 2.0：这个树：
```xml

50 
/   \
29     77
/  \     \
8   44     82
/ \         /\
2  10       80 99
/\    \
1  5   11
/      \
4       12
/          \
3           13
```

后继节点是什么意思？比如这棵树用中序遍历列出来是如下数列：

```java
「 1 2 3 4 5 8 10 11 12 13 29 44 50 77 82 」
```

8的后继节点就是10，29后继节点就是44，82的后继节点就是null；

但是我们要如何取出某个节点的后继节点呢？不可能每次都去遍历存个集合取相邻元素吧，这样是不科学的。

### 2.1.**那么科学的姿势是：**

1：**如果node结点有右子树，返回该右子树上的最小值**（即挂在该右子树上的左子树的最深元素）**若没有左子树则返回该元素本身**（因其右子树肯定比其本身要大））
> 比如77，它有右子树82，返回82这棵树上的最小值，由于82这棵树上没有左子树，所以其最小值就是其本身，所以77的后继节点为82

2：如果node结点没有右子树，那么要分两种情况: 

>
>a>如果node结点没有右子树,判断当前结点node是不是它父亲的左孩子，如果是，那么它父亲就是它的后继；
比如：1，它首先没有右子树，然后当前节点1又是它父亲的左孩子，所以它的后继就是它的父亲2.

>
>b>如果node结点没有右子树,判断当前结点是不是它父亲的右孩子，如果是，那么一路往父节点方向回溯，直到找到某一个父节点(ParentA)是其自己父节点(ParentA's Fathter)的左子树，那么这个ParentA's Fathter就是当前节点Node的后继节点
比如：13，它没有右子树，且是它自己父节点（12）的右孩子，那么开始回溯，回溯到它的父节点12，12并非它自己父节点的左子树，于是继续回溯，11，11也不是它自己父节点的左子树，于是再回溯，10，同样不是，再回溯，8是它自己父节点(29)的左子树，这里的8相当于上面说的"某个父节点ParentA"，这里的29相当于上面说的PaerentA's Father，所以由此可以推出29是13的后继节点。

## 3.代码怎么写？
说了这么多还是要落实到代码上。

```java
/**
* 获取指定元素的后继节点
* @return
*/
public Node getSuccessorNode(BinarySearchTree.Node node) {

if(node == null)
return null;

if(node.right == null){//如果该节点没有右子树,那么要分两种情况:
//a>如果node结点没有右子树,判断当前结点node是不是它父亲的左孩子，如果是，那么它父亲就是它的后继；
//比如：1，它首先没有右子树，然后当前节点1又是它父亲的左孩子，所以它的后继就是它的父亲2.

if (null != node.parent.left && node.parent.left.value.compareTo(node.value) == 0) {//node是不是它父亲的左孩子
//那么它父亲就是它的后继；
return node.parent;
} else if (null != node.parent.right && node.parent.right.value.compareTo(node.value) == 0) {//b>如果node结点没有右子树, 判断当前结点是不是它父亲的右孩子，
return getParentFather(node);
} else {
System.out.println("异常：getSuccessorNode");
}

}else {//如果该节点有右子树
//返回该右子树上的最小值（即左子树的最深元素）若没有左子树则返回该元素本身（因其右子树肯定比其本身要大））
if(node.right.left == null)
return node.right;

return getLeftMost(node.right.left);
}

return null;
}

//返回该右子树上的最小值（即左子树的最深元素）
private Node getLeftMost(Node node) {

if(node.left == null){
return node;
}

return getLeftMost(node.left);
}


/**
* 如果是，那么一路往父节点方向回溯，直到找到某一个父节点(ParentA)是其自己父节点(ParentA’s Fathter)的左子树，
* 那么这个ParentA’s Fathter就是当前节点Node的后继节点
* @param node
* @return
*/
private Node getParentFather(Node node) {
if(null != node.parent.parent.left && node.parent.value.compareTo(node.parent.parent.left.value) == 0){
return node.parent.parent;
}

return getParentFather(node.parent);
}


Client测试代码：

BinarySearchTree bst = new BinarySearchTree();
bst.add(50);
bst.add(29);
bst.add(8);
bst.add(2);
bst.add(5);
bst.add(4);
bst.add(3);
bst.add(44);
bst.add(77);
bst.add(82);
bst.add(10);
bst.add(1);
bst.add(11);
bst.add(12);
bst.add(13);

bst.traverse();
BinarySearchTree<Integer>.Node qNode = new BinarySearchTree<Integer>().new Node(new Integer(13));
BinarySearchTree.Node successorNode = bst.getSuccessorNode(bst.getOnEle(qNode));
System.out.println(qNode.value + " 的后继节点是：  "+successorNode.value);
//        bst.remove(new Integer(13));
bst.traverse();


输出结果：
13 的后继节点是：  29

```