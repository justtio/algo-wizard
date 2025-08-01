## LRU缓存机制
[LeetCode原题链接](https://leetcode.cn/problems/lru-cache/)

请你设计并实现一个满足  LRU (最近最少使用) 缓存 约束的数据结构。
实现 LRUCache 类：
LRUCache(int capacity) 以 正整数 作为容量 capacity 初始化 LRU 缓存
int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
void put(int key, int value) 如果关键字 key 已经存在，则变更其数据值 value ；如果不存在，则向缓存中插入该组 key-value 。如果插入操作导致关键字数量超过 capacity ，则应该 逐出 最久未使用的关键字。
进阶：函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。
```
示例：
输入
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
输出
[null, null, null, 1, null, -1, null, -1, 3, 4]

解释
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // 缓存是 {1=1}
lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
lRUCache.get(1);    // 返回 1
lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
lRUCache.get(2);    // 返回 -1 (未找到)
lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
lRUCache.get(1);    // 返回 -1 (未找到)
lRUCache.get(3);    // 返回 3
lRUCache.get(4);    // 返回 4
 
提示：
1 <= capacity <= 3000
0 <= key <= 10000
0 <= value <= 105
最多调用 2 * 105 次 get 和 put
```
咱们先不看题，先进入我们的邪修领域，假设你是一个非常热爱读书、热爱摄入各种“知识”的三好青年，你家里有个非常大的异次元书架（书架上各类书籍一应俱全），以及一个非常小且脆的书桌，只能叠放下三本书--三本你近期的心头好，最近时常拿起来研读的那种。
好，现在让我们将视线移至你的书桌，现在书桌上从上至下叠放着书籍如下：

《白毛的诱惑》|《丝袜的多重用途》|《邂逅兔耳娘》 

今晚你突然想研究一下如何邂逅兔耳娘，于是乎你就把它从底部抽了出来，研读一晚上后将其放回了书堆，此时书桌上三本书的是：

《邂逅兔耳娘》|《白毛的诱惑》|《丝袜的多重用途》

翌日，你又想研究一下如何使用丝袜，你就又把丝袜抽了出来，读完之后受益良多，常读常新，你便心满意足的将其放回了书桌，此时书桌的状态是：

《丝袜的多重用途》|《邂逅兔耳娘》|《白毛的诱惑》

终有一日，你在你的异次元书架上发现了一本神书：《母猪的产后护理》，你细细研读，顿感《白毛》好像没啥意思，你就把它抽出来放回书架，把《母猪》放到了桌上，这个时候书桌上的书变成了：

《母猪的产后护理》|《丝袜的多重用途》|《邂逅兔耳娘》

其实上述整个流程就是LRU缓存的原理，LRU(Least Recently Use)，现在来让我们回到这道算法题。
题目要求我们构建一个LRUCache类，其中包含，get，put以及display（就是展示最后数据啥样）等方法。我们来试着用JS代码实现一下，应该是不难的。

```js
//关键在于整个增删过程中数据必须保持规则上的有序，因此我们便想到使用Map数据结构来实现应该挺合适的
class LRUCache {
 constructor(capacity) {
  this.capacity = capacity //这个表示书桌上到底能放几本书
  this.cache = new Map(); //这个是记录书桌上的书到底是什么叠放顺序
 }

 get(key) {
  //从书桌取书的时候，要看书桌上有没有，有的话就取出来，看完再放回所有书的最上面
  if(this.cache.has(key)) {
   const value = this.cache.get(key); //这个get函数是map数据结构固有方法
   this.cache.delete(key); //从书桌取书
   this.cache.set(key, value); //重新放回去
   return value; //返回书名
  }
  //没有相关书籍的话
  return -1;
 }

 //把书放到书桌的操作
 put(key, value) {
  if(this.cache.has(key){
   this.cache.delete(key);
  }else if(this.capacity <= this.cache.size) {
   //桌上书满了，想放新书，就需要把你最长时间没看的书放回书架了
   //找到map中的第一个值，即书桌上最后一本书
   const deledKey = this.cache.keys().next().value;
   this.cache.delete(firstKey);
  }
  this.cache.set(key, value);
 }

 //查看当前桌上的书本状态
 display() {
  console.log('当前缓存：’， Array.from(this.cache.entries()));
 }
}
```

看，这样我们就设计了一个符合要求的LRU缓存了，是不是还挺简单的。用Map的特性来帮助我们实现是很巧妙的实现方式，也是相对优雅的实现，他俩真的很合适，当然也有很多人会用`双向链表 + 哈希表`来实现，也是利用的链表有“方向tag”这一特性，我们今天也来看一下这种该怎么实现吧

```js
//先构造双向链表的Node结构
class Node {
 constructor(key, value) {
  this.key = key;
  this.value = value;
  this.prev = null; 
  this.next = null;
 }
}

class LRUCacheLinkedList {
 constructor(capacity) {
  this.capacity = capacity;
  this.cache = new Map();

  //创建虚拟头节点，为了操作行为更加统一
  this.head = new Node(0, 0);
  this.tail = new Node(0, 0);
  this.head.next = this.tail;
  this.tail.prev = this.head;
 }

 //添加节点到头部（最新使用）,感觉不好理解的其实可以画个图
 //其实就是空双向链表插入新节点步骤
 addToHead(node) {
  //修改node节点指向
  node.prev = this.head;
  node.next = this.head.next;
  this.head.next.prev = node; //将tail指向node
  this.head.next = node; //将head指向node
 }

 //删除节点
 removeNode(node) {
  node.prev.next = node.next;
  node.next.prev = node.prev;
 }

 //移动节点到头部
 moveToHead(node) {
  this.removeNode(node);
  this.addToHead(node);
 }

 //删除尾部节点
 removeTail() {
  const lastNode = this.tail.prev; //尾节点是我们的辅助节点，所以真实情况下尾节点就是辅助节点的前一个节点
  this.removeNode(lastNode);
  return lastNode;
 }

 get(key) {
  const node = this.cache.get(key);
  if(node) {
   // 将节点移动到头部，标记为最新
   this.moveToHead(node);
   return node.value;
  }
  return -1;
 }

 put(key, value) {
  const node = this.cache.get(key);

  if(node) {
   node.value = value;
   this.moveToHead(node);
  }else {
   const newNode = new Node(key, value);

   if(this.cache.size >= this.capacity){
    //删除最久没有使用的节点
    const tail = this.removeTail();
    this.cache.delete(tail.key);
   }
   this.addToHead(newNode);
   this.cache.set(key, newNode);
  }
 }

 display() {
  const result = [];
  let current = this.head.next;
  //遍历这个链表
  while(current != this.tail) {
   result.push(`${current.key}:${current.value}`);
   current = current.next;
  }
  console.log('当前缓存：', result.join(' -> '));
 }
}
```

你看这个我们也实现完了，是不是感觉复杂了很多，但其实他们构建LRU缓存的核心原理是一样的，只不过是用的不同的数据结构，觉得双向链表这个太复杂了也没有关系，不用马上理解，但是请做最大的努力尝试一下，因为链表真的是非常基础且无比重要的一个数据结构。是所有学算法的小伙伴一定要掌握的，不过这不是这篇文章的重点，等我们以后经常跟链表打照面，咱们再好好说说。

这里有比较爱思考的邪修就会想，那我不依赖map和链表这样的数据结构，只用单纯数组和对象能不能实现呢，其实也是可以的，我们接下来就来一起看看。

```js
class SimpleLRUCache {
 constructor(capacity) {
  this.capacity = capacity;
  this.keys = []; //记录使用顺序的
  this.values = {}; //用来储存键值对的
 }

 get(key) {
  if(key in this.values) {
   this.keys = this.keys.filter(k => k != key);
   this.keys.push(key);
   return this.values[key];
  }
  return -1;
 }

 put(key, value) {
  if(key in this.values) {
   this.keys = this.keys.filter(k => k != key);
  }else if(this.key.length >= this.capacity) {
   const oldKey = this.keys.shift();
   delete this.values[oldKey];
  }
  this.keys.push(key);
  this.values[key] = value;
 }

 display() {
  const result = this.keys.map(key => `${key}:${this.values[key]}`);
  console.log(`当前缓存：${result.join(' -> ')}`);
 }
}
```

到这里，咱们构建LRUCache的旅途就要结束了，其实`最近最少使用`这个概念在我们生活也非常的常见，比如：

- 浏览器的历史记录
- 我们做收纳时，常用物品都会放在最顺手的地方
- 最近使用过的表情包、小程序也是这个逻辑
- 以及咱们手机的通话记录

所以你看，生活的处处场景中其实都会有算法的影子，算法其实离我们很近，我们有些习惯行为其实跟很多算法理念也是高度一致的，只是我们还不了解，也没有发觉，所以热爱读书的你，最喜欢的一本书是什么呢？

跟着算法邪修一起来学算法吧，让我们一探这个世界的“底层逻辑”！
