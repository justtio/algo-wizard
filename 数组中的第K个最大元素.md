## 数组中的第k个最大元素

给定整数数组 nums 和整数 k，请返回数组中第 k 个最大的元素。
请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

```
示例 1：
输入：nums = [3,2,1,5,6,4], k = 2
输出：5

示例 2：
输入：nums = [3,2,3,1,2,4,5,5,6], k = 4
输出：4
 
提示：

1 <= k <= nums.length <= 104
-104 <= nums[i] <= 104
```

```js
//排序法
var findKthLargest = function(nums, k) {
  //让数组从大到小排序
  nums.sort((a, b) => b - a);
  //去第k个数，下标就是k-1
  return nums[k - 1];
}
```
时间复杂度：O(nlogn),因为排序需要O(nlogn)
空间复杂度：O(1)

使用JS内置的sort，这道题是不是简简单单。接下来好学的我们再来看一下利用堆这个数据结构来试着跟这道题磨合一下，看看来说是不是更合适，准确的来说是最小堆。我们维护一个大小为k的最小堆，这样堆顶的元素就是我们想要的那个元素。我们一起来用代码看一下。

```js
//首先构建一个最小堆
class MinHeap{
  constructor() {
    this.heap = [];
  }

  //获取父节点索引
  //堆本质上是完全二叉树，父节点索引用于调整堆结构
  getParentIndex(index) {
    return Math.floor((index - 1) / 2);
  }

  //获取左子节点索引
  getLeftIndex(index) {
    return 2 * index + 1;
  }

  //获取右子节点索引
  getRightIndex(index) {
    return 2 * index + 2;
  }

  //交换两个节点
  swap(i, j) {
    [this.heap[i], this.heap[j]] = [this.heap[j], this.heap[i]];
  }

  //向上调整
  //插入新元素可能破坏最小堆性质，需要向上调整
  heapifyUp(index) {
    while(index > 0) {
      const parent = this.getParentIndex(index);
      //如果当前节点比父节点小，交换
      if(this.heap[index] < this.heap[parent]) {
        this.swap(index, parent);
        index = parent; //继续向上
      } else {
        break; //满足最小堆性质，停止
      }
    }
  }

  //向下调整堆
  // 删除堆顶后，最后一个元素补到堆顶，可能破坏堆结构，
  // 需要向下调整
  heapifyDown(index) {
    const left = this.getLeftIndex(index);
    const right = this.getRightIndex(index);
    let smallest = index;

    //找到左右子节点中比较小的那个
    if(left < this.heap.length && this.heap[left] < this.heap[smallest]) {
      smallest = left;
    }

    if(right < this.heap.length && this.heap[right] < this.heap[smallest]) {
      smallest = right;
    }

    //如果最小值不是当前节点，交换并继续向下调整
    if(smallest !== index) {
      this.swap(index, smallest);
      this.heapifyDown(smallest);
    }
  }

  //插入新元素
  insert(value) {
    this.heap.push(value); //先加到末尾
    this.heapifyUp(this.heap.length - 1); //再向上调整
  }

  //删除堆顶元素
  pop() {
    if(this.heap.length === 0) return null;
    if(this.heap.length === 1) return this.heap.pop();
    const top = this.heap[0];
    this.heap[0] = this.heap.pop(); //最后一个元素补到堆顶
    this.heapifyDown(0);
    return top;
  }

  peek() {
    return this.heap[0];
  }

  size() {
    return this.heap.length;
  }
}

//这样我们的最小堆就构建完成了
//接下来让我们看看怎么利用这个数据结构来写这个题解

var findKthLargest = function(nums, k) {
  //创建一个最小堆，容量为k
  const minHeap = new MinHeap();

  //遍历数组
  for(let num of nums) {
    //如果堆还没满，直接插入
    if(minHeap.size() < k) {
      minHeap.insert(num);
    }else if (num > minHeap.peek()) {
      //堆满了，且新元素比堆顶大，说明新元素更有资格成为前k大
      minHeap.pop();
      minHeap.insert(num);
    }
    //新元素比堆顶小就不用看了
  }
  return minHeap.peek();
}
```

