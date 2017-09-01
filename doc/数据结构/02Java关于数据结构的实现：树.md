# Java关于数据结构的实现：树

**关于作者**

>郭孝星，非著名程序员，主要从事Android平台基础架构与中间件方面的工作，欢迎交流技术方面的问题，可以去我的[Github](https://github.com/guoxiaoxing)提交Issue或者发邮件至guoxiaoxingse@163.com与我联系。

**文章目录**

- 一 树的概念与应用场景
    - 1.1 二叉查找树
    - 1.2 平衡二叉树
    - 1.3 B树
    - 1.4 红黑树
- 二 树的操作与源码实现
    - 2.1 TreeMap实现原理
    - 2.2 TreeSet实现原理

## 一 树的概念与应用场景

>树是一种抽象数据类型（ADT）或是实作这种抽象数据类型的数据结构，用来模拟具有树状结构性质的数据集合。它是由n（n>0）个有限节点组成一个具有层次关系的集合。把
它叫做“树”是因为它看起来像一棵倒挂的树，也就是说它是根朝上，而叶朝下的。

树有以下特点：

- 每个节点有零个或多个子节点；
- 没有父节点的节点称为根节点；
- 每一个非根节点有且只有一个父节点；
- 除了根节点外，每个子节点可以分为多个不相交的子树；

与树相关的概念

- 节点的度：一个节点含有的子树的个数称为该节点的度；
- 树的度：一棵树中，最大的节点的度称为树的度；
- 叶节点或终端节点：度为零的节点；
- 非终端节点或分支节点：度不为零的节点；
- 父亲节点或父节点：若一个节点含有子节点，则这个节点称为其子节点的父节点；
- 孩子节点或子节点：一个节点含有的子树的根节点称为该节点的子节点；
- 兄弟节点：具有相同父节点的节点互称为兄弟节点；
- 节点的层次：从根开始定义起，根为第1层，根的子节点为第2层，以此类推；
- 深度：对于任意节点n, n的深度为从根到n的唯一路径长，根的深度为0；
- 高度：对于任意节点n, n的高度为从n到一片树叶的最长路径长，所有树叶的高度为0；
- 堂兄弟节点：父节点在同一层的节点互为堂兄弟；
- 节点的祖先：从根到该节点所经分支上的所有节点；
- 子孙：以某节点为根的子树中任一节点都称为该节点的子孙。
- 森林：由m（m>=0）棵互不相交的树的集合称为森林；

注：参照亲戚关系，这些概念很好理解，家族谱也是一种树结构。😀

树的分类

- 无序树：树中任意节点的子节点之间没有顺序关系，这种树称为无序树，也称为自由树；
- 有序树：树中任意节点的子节点之间有顺序关系，这种树称为有序树；

其中有序树又分为：

- 二叉树：每个节点最多含有两个子树的树称为二叉树；
- 完全二叉树：对于一颗二叉树，假设其深度为d（d>1）。除了第d层外，其它各层的节点数目均已达最大值，且第d层所有节点从左向右连续地紧密排列，这样的二叉树被称为完全二叉树；
- 满二叉树：所有叶节点都在最底层的完全二叉树；
- 平衡二叉树（AVL树）：当且仅当任何节点的两棵子树的高度差不大于1的二叉树；
- 二叉查找树：树中的每个节点，它的左子树中所有项小于X中的项，它的右子树中的所有项大于X中的项。
- 霍夫曼树：带权路径最短的二叉树称为哈夫曼树或最优二叉树；
- B树：一种对读写操作进行优化的自平衡的二叉查找树，能够保持数据有序，拥有多余两个子树。


### 1.1 二叉查找树

### 1.1 平衡二叉树

### 1.1 B树

### 1.1 红黑树

>红黑树是平衡二叉树的变种，它的操作的时间复杂度是O(logN).

红黑树是一种具有着色性质的树，具有以下特点：

- 每个节点被着成红色或者黑色
- 根是黑色的
- 如果一个节点是红色的，那么他额子节点必须是黑色的。
- 从一个节点到一个null引用的每一条路径必须包含相同数目的黑色节点。

## 二 树的操作与源码实现

在文章[01Java关于数据结构的实现：表、栈与队列](https://github.com/guoxiaoxing/data-structure-and-algorithm/blob/master/doc/数据结构/01Java关于数据结构的实现：表、栈与队列.md)中我们
讨论了ArrayList与LinkedList的实现，它们的瓶颈在于查找效率低下。因而Java集合设计了Set与Map接口，它们在插入、删除与查找等基本操作都有良好的表现。

### 2.1 TreeMap实现原理

>TreeMap是一个基于红黑树实现的集合，它可以对里面的元素进行排序。

我们先来看看它的成员变量

```java
//比较器
private final Comparator<? super K> comparator;
//根节点
private transient TreeMapEntry<K,V> root = null;
//集合大小
private transient int size = 0;
//修改次数
private transient int modCount = 0;
```

TreeMap里面定义了静态内部类TreeMapEntry来描述节点信息。

```java
   static final class TreeMapEntry<K,V> implements Map.Entry<K,V> {
        K key;
        V value;
        TreeMapEntry<K,V> left = null;
        TreeMapEntry<K,V> right = null;
        TreeMapEntry<K,V> parent;
        boolean color = BLACK;

        /**
         * Make a new cell with given key, value, and parent, and with
         * {@code null} child links, and BLACK color.
         */
        TreeMapEntry(K key, V value, TreeMapEntry<K,V> parent) {
            this.key = key;
            this.value = value;
            this.parent = parent;
        }

        /**
         * Returns the key.
         *
         * @return the key
         */
        public K getKey() {
            return key;
        }

        /**
         * Returns the value associated with the key.
         *
         * @return the value associated with the key
         */
        public V getValue() {
            return value;
        }

        /**
         * Replaces the value currently associated with the key with the given
         * value.
         *
         * @return the value associated with the key before this method was
         *         called
         */
        public V setValue(V value) {
            V oldValue = this.value;
            this.value = value;
            return oldValue;
        }

        public boolean equals(Object o) {
            if (!(o instanceof Map.Entry))
                return false;
            Map.Entry<?,?> e = (Map.Entry<?,?>)o;

            return valEquals(key,e.getKey()) && valEquals(value,e.getValue());
        }

        public int hashCode() {
            int keyHash = (key==null ? 0 : key.hashCode());
            int valueHash = (value==null ? 0 : value.hashCode());
            return keyHash ^ valueHash;
        }

        public String toString() {
            return key + "=" + value;
        }
    }
```

### 2.2 TreeSet实现原理



