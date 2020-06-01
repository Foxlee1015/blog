---
layout: post
title: LinkedList
date: 2020-06-01 12:00:00 +0300
description: "LinkedList"
tags: [algorithm, linkedList]
img: linkedlist.png
---

## LinkedList

* 개념, 값과 다음 노드를 가르키는 데이터를 포함하는 객체(노드 = 값 + 다음 노드에 대한 정보)

{% highlight ruby %}
* LinkedList 1 -> 2 -> 3 -> 4 는 결국 
{ 
    value: 1, 
    next: { 
        value: 2, 
        next: { 
            value: 3, 
            next: {
                value: 4, 
                next: null
            } 
        }
    }
}

{% endhighlight %}

![참고 이미지]({{site.baseurl}}/assets/img/linkedlist.png)

{% highlight ruby %}


# Leetcode 206. Reverse Linked List

* Input: 1->2->3->4->5->NULL
* Output: 5->4->3->2->1->NULL


# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next

# Iterative
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        revers = None # 결과값 노드
        
        while head:        # iteration   # 1st           # 2nd
            cur = head                   # 1               2
            head = head.next             # 2               3                # 다음 노드로 이동
            cur.next = revers            # 1 + none        2 +  1 -> none   # 핵심: 원래 노드의 가장 왼쪽을 오른쪽으로 보냄(cur + revers)
            revers = cur                 # 1 -> none       2 -> 1 -> none
       
        return revers


# Recursive 
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        return self._reverse(head)
                                            # Recursion   # 1st                   # 2nd
    def _reverse(self, node, prev=None):                  # (head, preve=None)    # (2->3->~, 1->none)
        if not node:                                      # node= 1->2->3~~       # node = 2->3->
            return prev              
        n = node.next                                     # n= 2->3->~            # n= 3->4->
        node.next = prev                                  # none= 1=>none         # node = 2 -> 1-> none
        return self._reverse(n, node)                     # (2->3->~, 1->none)    # (3->4->, 2 -> 1-> none)

{% endhighlight %}



{% highlight ruby %}

# 876. Middle of the Linked List
* Given a non-empty, singly linked list with head node head, return a middle node of linked list.
* If there are two middle nodes, return the second middle node.
* Input: [1,2,3,4,5]
* Output: [3]
* Input: [1,2,3,4,5,6]
* Output: [4]

* slow(walker) & fast(runner) pointers

# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next

class Solution:
    def middleNode(self, head: ListNode) -> ListNode:
        walker = runner = head
                                                       1st      2nd       3rd
        while runner.next and runner.next.next:        2 & 3    4 & 5     x 
            walker = walker.next                       2         3
            runner = runner.next.next                  3         5
        
        if runner.next:                                                            6(6까지)
            return walker.next                                                     4
        else:
            return walker                                                 3(5까지)


def middleNode(self, head):
        slow = fast = head
        while fast and fast.next:            1 & 2      3 & 4    x(5까지)   5 & 6  
            slow = slow.next                 2          3                    4
            fast = fast.next.next            3          5                    null
        return slow                                              3           4



{% endhighlight %}