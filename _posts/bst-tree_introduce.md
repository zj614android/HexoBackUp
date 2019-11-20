------
layout: pages
title: bst-tree_introduce
date: 2019年06月06日14:25:24
tags: 树
categories: 数据结构/算法
------


## 1.什么是BST树

Bst树又名二分搜索树，binary search tree，它是遵循一定规律的二叉树；

- 1.二分搜索树是二叉树；
- 2.二分搜索树每个节点的值：
a.大于其左子树所有节点的值；
b.小于其右子树所有节点的值；
- 3.每一棵子树也是二分搜索树；

如图：
![](https://ws2.sinaimg.cn/large/006tNbRwly1fxlgub27ryj30db08sdi5.jpg)

就是说每往一个根节点添加元素时，左孩子要比根节点小，右孩子要比根节点大，不允许元素重复（可选项）；


## 2.手写一个基本的BST树

下面开始用java写一个Bst树，主要分为几步：
- 基本节点
- add元素流程
- query元素流程 //todo
- 遍历（先中后序）
- 删除元素 //todo

另外补充下：为何说是"基本的"，因为还存在可优化的情况，但是不揉在一起，先把最基本的怼出来，下一小节再来考虑优化问题（基本流程，递归）；

### 2.1.定义基本节点
定义好左孩子右孩子，根节点；

```java
public class BinarySearchTree<T extends Comparable> {

Node root;
int size;

class Node {

Node left;
Node right;
T value = null;

public Node(T value) {
this.value = value;
}

}

}
```

### 2.2.add元素流程
1.如果root为空则直接new一个，并将值添加给该节点；
2.如果不为空，则将值添加进节点，根据之前描述的二分搜索树的特性，左孩子比根小，右孩子比根大，那么就涉及对比（这里用的java泛型+比较器的方式），那么就需要另开一个函数接受一个Node和新传进来的值进行对比，就是下面代码的私有方法```nodeadd()```；

![](https://ws1.sinaimg.cn/large/006tNbRwly1fxll4e1q30j30tc1r40vk.jpg)

```java
public class BinarySearchTree<T extends Comparable> {

......

class Node {
......
}

public void add(T value) {

if (root == null) {
root = new Node(value);//1
size++;
return;
} else {
nodeAdd(root, value);//2
}

}

/**
* @param currNode 当前根节点
* @param newValue 新插入元素
*/
/**
* 二分搜索树每个节点的值：
* a.大于其左子树所有节点的值；
* b.小于其右子树所有节点的值；
*/
private void nodeAdd(Node currRoot, T newValue) {

//无重复元素
if (currRoot.value.compareTo(newValue) == 0) {
return;
}

//compareTo 前一个元素和后一个元素比较--返回整数，1，0,-1；返回1表示大于，返回-1表示小于，返回0表示相等；
if (currRoot.value.compareTo(newValue) > 0) {//左插

//当前根节点大于插入值，且当前根节点的左子树为空，所以该值应该插入左子树
if (currRoot.left == null) {
currRoot.left = new Node(newValue);
size++;
return;
} else {
//当前根节点大于插入值，且当前根节点的左子树不为空，则递归到一次的左子树查询当中去
nodeAdd(currRoot.left, newValue);//递归
}
} else if (currRoot.value.compareTo(newValue) < 0) {//右插

//右插同理左插
if (currRoot.right == null) {
currRoot.right = new Node(newValue);
size++;
return;
} else {
nodeAdd(currRoot.right, newValue);//递归
}

}

}

}
```

### 2.3.add流程优化

```java
......

private void nodeAdd(Node currRoot, T newValue) {

if (currRoot.value.compareTo(newValue) > 0) {//左插

//当前根节点大于插入值，且当前根节点的左子树为空，所以该值应该插入左子树
if (currRoot.left == null) {
currRoot.left = new Node(newValue);
size++;
return;
} else {
//当前根节点大于插入值，且当前根节点的左子树不为空，则递归到一次的左子树查询当中去
nodeAdd(currRoot.left, newValue);//递归
}
}
......
}

......

```
**问题出在哪？为什么要优化？要优化哪儿？**
1.终止条件臃肿
2.外部干预，外层调用的add方法会初始化树根节点root
3.没有访问到null节点（在访问之前就已经进行了判断）
综合起来就是"不美"；     ("▔㉨▔)!



**优化思路**
主要解决矛盾：解除外部干预，终止条件简化；
考虑方向：让元素先到空节点，如果是空节点必然就插入元素作为新根，否则再进行递归
产生的问题：树结构的维护；

**具体实现**


```java
public void add(T value) {
root = nodeAdd(root, value);
}

/**
* @param currRoot 当前根节点
* @param newValue 新插入元素
*/
private Node nodeAdd(Node currRoot, T newValue) {

if(currRoot == null){
size++;
return new Node(newValue);
}


if (currRoot.value.compareTo(newValue) > 0) {//左插
？=  nodeAdd(currRoot.left, newValue);//1
} else if (currRoot.value.compareTo(newValue) < 0) {//右插
？=  nodeAdd(currRoot.right, newValue);//递归
}

return currRoot;

}
```

如上代码是个未完成的代码，主要体现问题所在，主要是递归的时候返回根节点并没有和整棵树关联起来，比如：
- 第一个元素进去，5，树根为空，new了一个Node返回并赋值给root，这一步没问题；
- 第二个元素进去，1，树根是5不为空，判断为左插，这时候递归就有问题

啥问题？看注释1处，nodeAdd(currRoot.left, newValue),currRoot是5的left，为空，在递归调用中会新创建一个Node，这个Node返回给的是递归之前的函数，也就是说，它仅仅创建了一个对象，而这个对象并没有挂载到整棵树上；在该函数末尾return到currRoot5实际上本该插入的子树却没有插入，到下一个元素进来判断它的左子树还是为空;

之前不出问题是因为，之前的root是全局变量，它创建新node的时候就可以做子树关联，而这个写法在它递归时找到null节点创建的节点没有做父节点关联；

那么要做关联该怎么做？递归调用时遇见null元素返回的地方就是注释1处，我们只需要在？处加上父节点绑定即可，如下：
```java

public void add(T value) {
root = nodeAdd(root, value);
}

/**
* @param currRoot 当前根节点
* @param newValue 新插入元素
*/
private Node nodeAdd(Node currRoot, T newValue) {

if(currRoot == null){
size++;
return new Node(newValue);
}

if (currRoot.value.compareTo(newValue) > 0) {//左插
currRoot.left = nodeAdd(currRoot.left, newValue);//递归
} else if (currRoot.value.compareTo(newValue) < 0) {//右插
currRoot.right = nodeAdd(currRoot.right, newValue);//递归
}

return currRoot;

}
```


### 2.4.遍历：(前中后序)遍历粗浅理解，以及递归实现

先中后序遍历主要是根据访问根节点时机来进行的，什么意思？

![](https://ws3.sinaimg.cn/large/006tNbRwly1fxlltzuvqcj30uc0foao8.jpg)

前序就是先访问根节点，再访问左节点（递归左子树），然后访问右节点（递归右子树）；
中序就是先访问左节点（递归左子树），再访问根节点，然后访问右节点（递归右子树）；
后序就是先访问右节点（递归右子树），再访问左节点（递归左子树），然后访问根节点；

**等于就是先序就是先访问根，中序就是根在中间，后续就是根在最后，且左子树永远要比右子树更优先遍历；**

贴上代码：

```java


public class BinarySearchTree<T extends Comparable> {

......

class Node {
......
}

public void add(T value) {

......
}


private void nodeAdd(Node currRoot, T newValue) {
......
}

/**
* 暴露给用户
*/
public void traverse() {
traversePre(root);
}

/**
* 先序遍历
*/
private void traversePre(Node node) {
if (node == null)
return;

System.out.println(node.toString());//访问元素
traversePre(node.left);
traversePre(node.right);
}


/**
* 中序遍历
*/
private void traverseMid(Node node) {

if (node == null)
return;

traversePre(node.left);
System.out.println(node.toString());//访问元素
traversePre(node.right);
}


/**
* 后序遍历
*/
private void traverseAfter(Node node) {

if (node == null)
return;

traversePre(node.left);
traversePre(node.right);
System.out.println(node.toString());//访问元素
}
}

```

### 2.5.验证测试

在客户端内添加一波元素看看效果；

```java
public class BstTest {

public static void main(String[] args) {

BinarySearchTree bst = new BinarySearchTree();
bst.add(5);
bst.add(1);
bst.add(3);
bst.add(6);
bst.add(6);
bst.add(7);
bst.add(11);
bst.add(1);

bst.traverse();
}
}
```

推测一下数据应该有的样子，然后再看输出的结果对不对的上；

![](https://ws1.sinaimg.cn/large/006tNbRwly1fxll4e1q30j30tc1r40vk.jpg)

打印结果：5,1,3,6,7,11
```java
value == 5：：：left value == 1__right value == 6
value == 1：：：left value ==  null __right value == 3
value == 3：：：left value ==  null __right value ==  null 
value == 6：：：left value ==  null __right value == 7
value == 7：：：left value ==  null __right value == 11
value == 11：：：left value ==  null __right value ==  null 
```

可以看见结果是能对的上的，但是，这只是最简单的理解；

## 3.深入理解BST树的遍历

### 3.1.（前中后序）遍历深入理解

之前讨论的前中后序遍历重点关注的只是结果，这一小节准备揪出这个遍历的本质，即当我们去遍历这个树时，每个节点具体的访问顺序是怎样的，这个就有点像是程序debug打断点，一步步的走；

还是这个树：
![](https://ws1.sinaimg.cn/large/006tNbRwly1fxll4e1q30j30tc1r40vk.jpg)

先说结论：在递归的前提下，每个节点都会被访问三次，用先序遍历举例，这个树实际节点访问的顺序是；

⑤①①③③③①⑤⑥⑥⑦⑦⑪⑪⑪⑦⑥⑤

一共6个元素，每个元素访问3次，总共18个步骤，接下来将一个个分析每一步的理由：

⑤：第一个元素开始，访问5，
①：由于是先序（后面将不再强调先序），访问5的左子树，于是来到1
①：1接着访问1点左子树，由于1没有左子树（null），于是返回1
③：接着访问1的右子树，就来到了3
③：接着访问3的左子树，为空，返回3
③：接着访问3的右子树，为空，返回3
①：3访问完了，返回父亲节点，也就是1
⑤：1访问完了，返回父亲节点，也就是树根节点5
⑥：5的左子树访问完了，于是访问5的右子树，于是来到6
⑥：6访问左子树，发现为空，返回自己，也就是6
⑦：6访问右子树，于是来到7
⑦：7访问左子树，发现为空，于是返回自己，也就是7
⑪：7访问右子树，于是来到11
⑪：11访问自己的左子树，发现为空，返回自己，也就是11
⑪：11访问自己的右子树，发现为空，返回自己，也就是11
⑦：11遍历完成之后，返回自己的父亲节点，也就是7
⑥：7遍历完成之后，返回自己的父亲节点，也就是6
⑤：6遍历完成之后，返回自己的父亲节点，也就是树根节点5，整个遍历结束；

在理解了每个节点的具体访问步骤之后，我们知道每个元素都会访问3次，也就是说，每个节点都会第一次被访问，第二次被访问，第三次被访问；

而先序列遍历就是在节点第一次被访问的时候进行数据处理（比如打印自己的值）；
中序遍历就是在节点第二次被访问的时候进行数据处理，同样后序遍历就是在节点第三次被访问的时候进行数据处理，**所以我们发现不同的遍历方式出来的值顺序不一样**；

知道了先序的这个流程之后，中后序根这个一个意思，就不写了，验证这个可以用后面贴出的Demo代码进行测试,这里也不赘述了；


### 3.2.（前中后序）遍历的非递归实现

非递归实现的核心思想就是用"循环+栈来替代递归"；
栈是后进先出，比如一个最简单的树：
```java
⑤
/\
①  ⑦
```
用先序举例，访问结果是517，那么stack实现的流程就是：
```java
//伪码
stack.push(5);
stack.pop();
stack.push(7);
stack.push(1);
stack.pop();
stack.pop();
```
理解了这一层之后来写代码：
```java
/**
* 先序遍历，非递归
*/
private void traversePre(Node rootnode) {

Stack<Node> stack = new Stack<>();
stack.push(rootnode);

while(!stack.isEmpty()){
Node currNode = stack.pop();

if(currNode == null)
return;
else
System.out.println(currNode);//访问元素

if(null != currNode.right)
stack.push(currNode.right);

if(null != currNode.left)
stack.push(currNode.left);
}

}
```
根据之前的结果结合这个代码来反推算一算：⑤①③⑥⑦⑪

**第一趟：**
- 5先入栈
- stack不为空，进入循环，从stack内拿出5，打印5（访问数据）
- 5的right，6，不为空，入栈
- 5的left，1，不为空，入栈
- 本趟结果：⑤
- 栈内情况：
```java
1
6
```

**第二趟：**
- stack不为空，进入循环，从stack内拿，不为空，拿到1，打印
- 1的right，3，不为空，入栈
- 1的left，为空，什么都不做
- 本趟结果：①
- 栈内情况：
```java
3
6
```

**第三趟：**
- stack不为空，进入循环，从stack内拿，不为空，拿到3，打印
- 3的right，为空，什么都不做
- 3的left，为空，什么都不做
- 本趟结果：③
- 栈内情况：
```java
6
```

**第四趟：**
- stack不为空，进入循环，从stack内拿，不为空，拿到6，打印
- 6的right，7，不为空，入栈
- 6的left，为空，什么都不做
- 本趟结果：⑥
- 栈内情况：
```java
7
```

**第五趟：**
- stack不为空，进入循环，从stack内拿，不为空，拿到7，打印
- 7的right，11，不为空，入栈
- 7的left，为空，什么都不做
- 本趟结果：⑦
- 栈内情况：
```java
11
```

**第六趟：**
- stack不为空，进入循环，从stack内拿，不为空，拿到11，打印
- 11的right，为空，什么都不做
- 11的left，为空，什么都不做
- 本趟结果：⑪
- 栈内情况：
```java
无
```

**第七趟：**
- stack为空，结束循环；


对比打印结果一致：
```java
//递归先序
value == 5：：：left value == 1__right value == 6
value == 1：：：left value ==  null __right value == 3
value == 3：：：left value ==  null __right value ==  null 
value == 6：：：left value ==  null __right value == 7
value == 7：：：left value ==  null __right value == 11
value == 11：：：left value ==  null __right value ==  null 

//非递归先序
value == 5：：：left value == 1__right value == 6
value == 1：：：left value ==  null __right value == 3
value == 3：：：left value ==  null __right value ==  null 
value == 6：：：left value ==  null __right value == 7
value == 7：：：left value ==  null __right value == 11
value == 11：：：left value ==  null __right value ==  null 
```

在验证了这个流程之后，中后序，无非就是调整入栈出栈时机的问题；

**总结一下就是：非递归遍历树借助了辅助容器（stack）来实现的，当要压进一个元素的意思就是我们即将访问这个元素；然后压栈的时候主要它先进后出的特性，顺序要反着压；**

**另外，我在想一个问题，bst树遍历非递归实现有何实际意义？**
后来度娘了一波，似乎是和性能有关系，意思就是递归耗性能：
```java
递归和非递归只是解决问题的方法的不同，本质还是一样的。
2. 递归算法相对于非递归算法来说效率通常都会更低
2.1 递归算法会有更多的资源需要压栈和出栈操作（不仅仅是参数，还有函数地址等）
2.2 由于编译器对附加的一些栈保护机制会导致递归执行的更加低效
3. 使用循环代替递归算法，通常可以获得更好的执行效率和空间效率，在二叉树层次较深的情况下，采用非递归方式遍历能够有效的提升遍历的性能。
```

## 4.Demo
https://github.com/zj614android/designPattern/tree/master/app/src/main/java/com/zj/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/%E6%A0%91/bst