# Step3 3回続けて10分以内に書いてエラーを出さなければOKとする
- 平均1:30秒位で書けた。
```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null) {
            return false;
        }
        Set<ListNode> visited = new HashSet<ListNode>();
        ListNode node = head;
        visited.add(node);
        while(node.next != null) {
            node = node.next;
            if (visited.contains(node)) {
                return true;
            }
            visited.add(node);
        }
        return false;
    }
}
```