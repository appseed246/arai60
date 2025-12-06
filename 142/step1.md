# Step1 何も見ずに解く
「141. Linked List Cycle」と同様のアルゴリズムで解答。
返り値がbooleanからListNodeに変わっただけ。5分ほどで解答。
計算量は、時間計算量:O(n)、空間計算量:O(n)
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
    public ListNode detectCycle(ListNode head) {
        Set<ListNode> visited = new HashSet<ListNode>();
        ListNode node = head;
        
        while (node != null) {
            if (visited.contains(node)) {
                return node;
            }
            visited.add(node);
            node = node.next;
        }

        return null;
    }
}
```