# step4 レビューを受けて解答を修正

コードの整え方を参考に、衝突点の検出の関数化でもやってみる。

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        // フロイドの循環検出アルゴリズムで探索する

        ListNode collisionPoint = findCollisionPoint(head);
        if (collisionPoint == null) {
            return null;
        }

        // 開始地点探索
        ListNode start = head; 
        while (collisionPoint != start) {
            collisionPoint = collisionPoint.next;
            start = start.next;
        }

        return start;
    }

    private ListNode findCollisionPoint(ListNode node) {
        ListNode fast = node;
        ListNode slow = node;
        while (fast != null && fast.next != null) {
            if (fast == slow) {
                return fast;
            }
            fast = fast.next.next;
            slow = slow.next;
        }

        return null;
    }
}
```