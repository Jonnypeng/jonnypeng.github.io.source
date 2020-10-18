---
title: 双指针删除链表的倒数第n个节点
date: 2020-10-18 11:31:28
tags:
- 链表
- 双指针
- 算法
---
### 题目
给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。

示例：

给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
说明：

给定的 n 保证是有效的。

进阶：

你能尝试使用一趟扫描实现吗？

<!-- more -->

[题目链接](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list)


### 解题思路
这里使用双指针，指针的位置为双指针的区间（0...n),先使用for循环找到0到n的指针，设置一个指针指向x=0，设置一个指针指向n，双指针以同样的速度进行移动，当n指针指向的节点的next为null的时候，就会停止，然后删除x指针指向的节点，双端进行拼接即可

### 代码

```javascript
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
var removeNthFromEnd = function (head, n) {
    //当节点只有一个的情况
  if (head.next === null) {
        head = null;
        return head;
    }
    //当节点只有两个，且n===1的情况
    if (head.next.next === null && n === 1) {
        head.next.next = null;
        head.next = null;
        return head;
    }

    //当节点只有两个，且n===2的情况
    if (head.next.next === null && n === 2) {
        let _head = head.next;
        head = null;
        head = _head;
        return head;
    }

    let prevNode;  //被删除节点的上一个节点
    let nextNode;   //被删除节点的下一个节点
    let deleteNode;  //被删除节点

    let firstNode = head;   //可以移动的x指针节点
    let pointer = head;    
    let lastNode;   //可以移动的最后一个指针节点
    //查找n指针
    for (let i = 0; i < n; i++) {
        lastNode = pointer;
        pointer = lastNode.next;
    }

    //双指针移动，并赋值给被可删除节点
    while (lastNode.next !== null) {
        prevNode = firstNode;
        firstNode = firstNode.next;
        nextNode = firstNode.next;
        lastNode = lastNode.next;
        deleteNode = firstNode.val;
    }

    if(prevNode!==undefined){
        prevNode.next = nextNode;
        prevNode.next = nextNode;
        firstNode = null;
    }else{
        let _firstNode = firstNode.next;
        firstNode = null;
        head = _firstNode;
        return head;
    }
   
    return head;
};
```
