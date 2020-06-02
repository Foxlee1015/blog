---
layout: post
title: LinkedList-medium
date: 2020-06-21 12:00:00 +0300
description: "LinkedList"
tags: [algorithm, linkedList]
img: linkedlist.png
---

## LinkedList - medium


{% highlight ruby %}
# Leetcode 86. Partition List
* Given a linked list and a value x, partition it such that all nodes less than x come before nodes greater than or equal to x.
* You should preserve the original relative order of the nodes in each of the two partitions.

* Input: head = 1->4->3->2->5->2, x = 3
* Output: 1->2->2->4->3->5


# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next

class Solution:
    def partition(self, head: ListNode, x: int) -> ListNode:
        
        if not head:
            return None
        
        dummyS = nodeSmall = ListNode(0)      # 처음 실수한 부분 // 노드 앞부분을 볼 dummyS, 노드 next를 볼 nodeSmall
        dummyB = nodeBig = ListNode(0)        #                    동일
        
        while head:
            if head.val < x:
                nodeSmall.next = head
                nodeSmall = nodeSmall.next
            else:
                nodeBig.next = head
                nodeBig = nodeBig.next
            head = head.next

        nodeBig.next = None                  # 마지막 부분 null 처리
        nodeSmall.next = dummyB.next         # nodeSmall 가장 마지막 next를 dummyB 앞에 연결( dummyB 가 아닌 dummyB.next인 이유는 HEAD 0를 뺴기 위함)
        return dummyS.next                   # dummyS head 0 다음 dummyS.next
{% endhighlight %}



{% highlight ruby %}

{% endhighlight %}