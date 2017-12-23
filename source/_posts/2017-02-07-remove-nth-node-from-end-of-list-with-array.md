---
title: Remove Nth Node From End of List 数组实现
permalink: remove-nth-node-from-end-of-list-with-array
date: 2017-02-07 01:54:30
updated:
tags: [LeetCode, Algorithm, JavaScript]
cover:
share_cover:
---

## 原题

Given a linked list, remove the nth node from the end of list and return its head.

For example,

```
Given linked list: 1->2->3->4->5, and n = 2.

After removing the second node from the end, the linked list becomes 1->2->3->5.
```

**Note:**

Given n will always be valid.

Try to do this in one pass.

## 数组方式解答

思路：先遍历整个链表，存成一个数组。然后删除从末尾起第 n 个元素（或从头起的第 `length - n + 1` 个元素）。然后遍历数组建立新的链表，然后返回此链表，结束程序。

我选择了从头算个数的方式，由于 Array 下标从 0 开始，因此传入的位置需要减 1，即最终传入 `length - n`。

```javascript
var removeNthFromEnd = function(head, n) {
    let arrayBridge = [];
    let currentNode = head;
  
  	// first pass: get the length
    while (currentNode) {
        arrayBridge.push(currentNode.val);
        currentNode = currentNode.next;
    }
  	
  	// for an edge situation where head has only 1 node
  	// leetcode expects an [] as an empty ListNode object
    if (n === 1 && n === arrayBridge.length) return [];

    arrayBridge.splice(arrayBridge.length - n, 1);

    let list = curr = new ListNode(arrayBridge[0]);
    let count = 1;

    while (count < arrayBridge.length) {
        curr.next = new ListNode(arrayBridge[count]);
        curr = curr.next;
        count++;
    }

    return list;
};
```

此解已是一个可获得 Accepted 的答案，但离题目要求的「只用一通」颇有差距，最坏的情况需要几乎两通的时间。

