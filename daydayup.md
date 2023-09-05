### 算法

#### [49. 字母异位词分组](https://leetcode.cn/problems/group-anagrams/)

- 关键点
  1. 利用map键的唯一性来存
  2. 怎么体现**字母异位词**的唯一性
     - 排序
     - 质数之积

#### [128.最长连续序列](https://leetcode.cn/problems/longest-consecutive-sequence/?envType=study-plan-v2&envId=top-100-liked)

- 关键点

  1. 时间复杂度要求O(n)，想到加空间

  2. 定义一个数据结构

     ~~~java
     //定义一个链表，链表的每个节点都是连续数的一个链表，有一个记录当前节点长度的属性
     class Node(){
         private int val;//头节点值
         private int len;//链表长度
         private Node next;
         private List<Node> nodes; 
     }
     ~~~

- 总结

  1. 思路不正确，这样子可以做但时间复杂度不是O(n)，因为遍历元素一个个比过去相当于O(n2)了
  2. 要想到用hash表，以空间换时间

#### [283.移动零](https://leetcode.cn/problems/move-zeroes/?envType=study-plan-v2&envId=top-100-liked)

- 关键点

  只需要计算前面出现0的次数，再让每个数前进这么多位即可

#### [11.盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/?envType=study-plan-v2&envId=top-100-liked)

- 关键点
  1. 最多水的容器就是 (r-l)*Math.min(r,l)
  2. r>l并且中间必须有nums[i]<nums[r],nums[l]
  3. nums[l]左边都比他小，nums[r]右边都比他小

### 学习

#### [图数据库选型比较：Neo4j、JanusGraph、HugeGraph](https://zhuanlan.zhihu.com/p/114834574)

#### [手把手教你快速入门知识图谱 - Neo4J教程](https://zhuanlan.zhihu.com/p/88745411)

#### WebSocket

#### chatGPT-Java

https://juejin.cn/post/7208907027841171512



### 整个服务器，建个新项目用来学习

各种登陆方式