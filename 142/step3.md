# Step3 3回続けて10分以内に書いてエラーを出さなければOKとする
フロイドの循環検出アルゴリズムはSWEの常識セットの範囲外とのことなので、Setによる解法で解く。
平均1分30秒で解答。
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