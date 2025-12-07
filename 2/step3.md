# Step3 3回続けて10分以内に書いてエラーを出さなければOKとする

## 解答
- 1回目: 3:17
- 2回目: 2:05
- 3回目: 2:03
- 平均: 2:28
```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode dummyHead = new ListNode();
        ListNode tail = dummyHead;

        ListNode l1Node = l1;
        ListNode l2Node = l2;
        int carry = 0;
        while (l1Node != null || l2Node != null || carry == 1) {
            if (l1Node == null) {
                l1Node = new ListNode(0);
            }
            if (l2Node == null) {
                l2Node = new ListNode(0);
            }

            int sum = l1Node.val + l2Node.val + carry;
            carry = sum / 10;

            tail.next = new ListNode(sum % 10);
            tail = tail.next;
            l1Node = l1Node.next;
            l2Node = l2Node.next;
        }

        return dummyHead.next;
    }
}
```