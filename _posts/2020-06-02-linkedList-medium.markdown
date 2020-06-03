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

-   Given a linked list and a value x, partition it such that all nodes less than x come before nodes greater than or equal to x.
-   You should preserve the original relative order of the nodes in each of the two partitions.

-   Input: head = 1->4->3->2->5->2, x = 3
-   Output: 1->2->2->4->3->5

# Definition for singly-linked list.

# class ListNode:

# def **init**(self, val=0, next=None):

# self.val = val

# self.next = next

class Solution:
def partition(self, head: ListNode, x: int) -> ListNode:

if not head:
return None

dummyS = nodeSmall = ListNode(0) # 처음 실수한 부분 // 노드 앞부분을 볼 dummyS, 노드 next를 볼 nodeSmall
dummyB = nodeBig = ListNode(0) # 동일

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

# 328. Odd Even Linked List

-   Given a singly linked list, group all odd nodes together followed by the even nodes. Please note here we are talking about the node number and not the value in the nodes.
-   You should try to do it in place. The program should run in O(1) space complexity and O(nodes) time complexity.

-   Input: 1->2->3->4->5->NULL
-   Output: 1->3->5->2->4->NULL

-   Input: 2->1->3->5->6->4->7->NULL
-   Output: 2->3->6->7->1->5->4->NULL

# Definition for singly-linked list.

# class ListNode:

# def **init**(self, val=0, next=None):

# self.val = val

# self.next = next

class Solution:
def oddEvenList(self, head: ListNode) -> ListNode:

oddDummy = oddNode = ListNode(0)
evenDummy = evenNode = ListNode(0)

while head:
oddNode.next = head # 현재 - oddNode = head 가 아닌 oddNode.next 로 해서 0 -> 1 이 됨
evenNode.next = head.next # 다음꺼 받음

oddNode = oddNode.next # 다음 노드로 이동
evenNode = evenNode.next # 다음 노드로 이동

head = head.next.next if evenNode else None # evenNode 는 현재 head.next

oddNode.next = evenDummy.next

return oddDummy.next

{% endhighlight %}

{% highlight ruby %}

# 24. Swap Nodes in Pairs

-   Given a linked list, swap every two adjacent nodes and return its head.
-   You may not modify the values in the list's nodes, only nodes itself may be changed

*   Given 1->2->3->4
*   you should return the list as 2->1->4->3.

# Definition for singly-linked list.

# class ListNode:

# def **init**(self, val=0, next=None):

# self.val = val

# self.next = next

class Solution:
def swapPairs(self, head: ListNode) -> ListNode:
if not head:
return None

dummy = p = ListNode(0)
dummy.next = head # dummy : 0 1 2 3 4

    while head and head.next:
        tmp = head.next            # tmp: 2* 3 4       # tmp: 4*  ( head: 3* 4)
        head.next = tmp.next       # head: 1* 3 4      # head: 3* null
        tmp.next = head            # tmp: 2* 1 3 4     # tmp:  4* 3 null
        p.next = tmp               # p: 0* 2 1 3 4     # p: 0 2 1* 4 3 null
        head = head.next           # head: 3* 4        # head: null
        p = tmp.next               # p: 0 2 1* 3 4     # // p: 0 2 1 4 3*
                                          (tmp.next)
    return dummy.next

{% endhighlight %}

{% highlight ruby %}

# 19. Remove Nth Node From End of List

-   Given linked list: 1->2->3->4->5, and n = 2.
-   After removing the second node from the end, the linked list becomes 1->2->3->5.

# Definition for singly-linked list.

# class ListNode:

# def **init**(self, val=0, next=None):

# self.val = val

# self.next = next

class Solution:
def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:

fast = slow = head # head 안에서 n 만큼 fast 이동 후 slow 와 fast 같이 이동
for \_ in range(n):
fast = fast.next # n 만큼 먼저 이동
if not fast: # n 만큼 이동 시
return head.next
while fast.next: # fast slow 같이 이동 후
fast = fast.next
slow = slow.next
slow.next = slow.next.next # 해당 노드 제거
return head

{% endhighlight %}
