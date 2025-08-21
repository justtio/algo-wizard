## 删除链表的倒数第N个节点

给你一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。

```
示例 1：
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]

示例 2：
输入：head = [1], n = 1
输出：[]

示例 3：
输入：head = [1,2], n = 1
输出：[1]

提示：

链表中结点的数目为 sz
1 <= sz <= 30
0 <= Node.val <= 100
1 <= n <= sz
 
进阶：你能尝试使用一趟扫描实现吗？
```

必须要找到目标节点的前一个节点才能进行删除操作

```js
/**
 * function ListNode(val, next) {
 *  this.val = (val === undefined ? 0 : val);
 *  this.next = (next === undefined ? null : next);
 * }
 */

var removeNthFromEnd = function(head, n) {
  //创建一个虚拟头节点，简化对头节点的删除操作
  const dummy = new ListNode(0, head);
  let slow = dummy;
  let fast = dummy;

  //快指针先走，移动n+1步
  //为什么是n+1呢，因为我们让fast和slow同时从dummy开始
  //fast先走n步，保证了它和slow之间相隔了n个节点
  //这样fast走到null时，slow正好在倒数第n+1个节点
  for(let i = 0; i < n; i++) {
    fast = fast.next;
  }

  //双指针同步前进
  //当fast到达链表末尾的null时，循环终止
  while(fast.next !== null) {
    fast = fast.next;
    slow = slow.next;
  }

  //删除倒数第N个节点
  slow.next = slow.next.next;

  //返回新链表
  return dummy.next;
}
```