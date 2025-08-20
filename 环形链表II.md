## 环形链表II

给定一个链表的头节点  head ，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。

如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。如果 pos 是 -1，则在该链表中没有环。注意：pos 不作为参数进行传递，仅仅是为了标识链表的实际情况。

不允许修改 链表。

```
示例 1：
输入：head = [3,2,0,-4], pos = 1
输出：返回索引为 1 的链表节点
解释：链表中有一个环，其尾部连接到第二个节点。

示例 2：
输入：head = [1,2], pos = 0
输出：返回索引为 0 的链表节点
解释：链表中有一个环，其尾部连接到第一个节点。

示例 3：
输入：head = [1], pos = -1
输出：返回 null
解释：链表中没有环。
 

提示：

链表中节点的数目范围在范围 [0, 104] 内
-105 <= Node.val <= 105
pos 的值为 -1 或者链表中的一个有效索引
 
进阶：你是否可以使用 O(1) 空间解决此题？
```

假设：
链表头到环入口的距离是L
环的长度是C
快慢指针在环内相遇时，它们的移动距离：
慢指针：L+a（a是环入口到相遇点的距离）
快指针：L+a+nC(n是快指针在环里多走的圈数)
因为快指针的速度是慢指针的两倍，所以快指针走的距离是慢指针的两倍：
2*(L + a) = L + a + nC => 
L + a = nC =>
L = (n - 1)C + (C - a)
所以C-a是从相遇点，顺着环再走一圈，回到环入口的距离我们称它为b
L = (n - 1)C + b


```js
/**
 * function ListNode(val) {
 * this.val = val;
 * this.next = null;
 * }
 */

var detectCycle = function(head) {
  //特殊情况判断
  if(!head || !head.next) {
    return null;
  }

  //第一步：用快慢指针寻找相遇点
  let slow = head;
  let fast = head;
  let hasCycle = false;

  while(fast && fast.next) {
    slow = slow.next;
    fast = fast.next.next;
    if(slow === fast) {
      hasCycle = true;
      break;
    }
  }

  //如果没有环直接返回null
  if(!hasCycle) return null;

  //寻找环的入口
  //一个指针从头部开始
  let ptr = head;
  //另一个指针从相遇点开始
  //它们以相同的速度前进，相遇点即为环的入口
  while(ptr !== slow) {
    ptr = ptr.next;
    slow = slow.next;
  }

  //返回环的入口节点
  return ptr;
}
```