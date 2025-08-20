## 合并K个升序链表

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

```
示例 1：
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6

示例 2：
输入：lists = []
输出：[]

示例 3：
输入：lists = [[]]
输出：[]
 

提示：
k == lists.length
0 <= k <= 10^4
0 <= lists[i].length <= 500
-10^4 <= lists[i][j] <= 10^4
lists[i] 按 升序 排列
lists[i].length 的总和不超过 10^4
```

```js
//辅助函数，合并两个有序链表
const mergeTwoList = (l1, l2) => {
  //虚拟头节点
  const dummy = new ListNode(0);
  let current = dummy;

  while(l1 && l2) {
    if(l1.val <= l2.val) {
      current.next = l1;
      l1 = l1.next;
    }else {
      current.next = l2;
      l2 = l2.next;
    }
    current = current.next;
  }
  //处理剩余部分
  current.next = l1 || l2;

  return dummy.next;
}

var mergeKLists = function(lists) {
  //如果链表为空，直接返回空
  if(!lists || lists.length === 0) return null;

  //核心递归函数：分治合并
  const merge = (start, end) => {
    //递归终止条件：只有一个链表，直接返回
    if(start === end) {
      return lists[start];
    }

    //如果没有链表
    if(start > end) {
      return null;
    }

    const mid = start + (end - start) >> 1;

    //递归合并左半部分
    const leftMerged = merge(start, mid);
    //递归合并右半部分
    const rightMerged = merge(mid + 1, end);

    //合并左右两部分的结果
    return mergeTwoLists(leftMerged, rightMerged); 
  }
  //启动分治
  return merge(0, lists.length - 1);
}
```

```js
//优先队列简化实现
class PriorityQueue {
  constructor(comparator = (a, b) => a - b) {
    this._heap = [];
    this._comparator = comparator;
  }

  //插入元素
  enqueue(item) {
    this._heap.push(item);
    this._heap.sort(this._comparator);
  }

  //取出堆顶元素
  dequeue() {
    if(this.isEmpty()) return null;
    return this._heap.shift();
  }

  //获取堆顶元素
  peek() {
    return this._heap[0] || null;
  }

  isEmpty() {
    return this._heap.length === 0;
  }
}

/**
 * function ListNode(val, next) {
 *  this.val = val;
 *  this.next = next;
 * }
 */

var mergeKLists = function(lists) {
  if(!lists || lists.length === 0) return null;

  //初始化优先队列，比较器为依据节点值大小排列
  const pq = new PriorityQueue((a, b) => a.val - b.val);

  //将所有链表的头节点放入优先队列
  for(const list of lists) {
    if(list) {
      pq.enqueue(list);
    }
  }

  const dummy = new ListNode(0);
  let current = dummy;

  //循环直到优先队列为空
  while(!pq.isEmpty()) {
    //取出值最小的节点
    const smallestNode = pq.dequeue();

    //将节点连接到结果链表上
    current.next = smallestNode;
    current = current.next;

    //如果该节点还有下一个节点，将其放入优先队列
    if(smallestNode.next) {
      pq.enqueue(smallestNode.next);
    }
  }
  return dummy.next;
}
```