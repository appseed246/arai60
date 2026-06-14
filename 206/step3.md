# Step3 3回続けて10分以内に書いてエラーを出さなければOKとする
50秒ほどでAccept

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode newHead = null;
        ListNode node = head;

        while (node != null) {
            ListNode rest = node.next;
            node.next = newHead;
            newHead = node;
            node = rest;
        }

        return newHead;
    }
}
```

再帰版も。約1:50でAccept。

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null) {
            return null;
        }

        ListNode newHead = head;
        ListNode tail = head.next;
        if (tail != null) {
            newHead = reverseList(tail);
            tail.next = head;
        }
        head.next = null;

        return newHead;
    }
}
```