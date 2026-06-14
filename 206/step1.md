# Step1 何も見ずに解く
+ 問題: 渡されたLinkedListを反転させる
+ 思いついた解法
  + 先頭から辿ってリストをコピーする方法
    + 手順
      + ノードの値を参照し、参照した値を持つ新しいノードを作成する
      + 次のノードを参照、新しいノードを作成し、1つ前のノードに紐づける
      + これを末尾まで繰り返す。
    + 時間計算量は O(n)、空間計算量は O(n)。最大のノード数は5000なので時間的には間に合う
+ 11:51で、解法検討 + 実装完了 Accept。

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
        ListNode lastSeenNode = null;
        ListNode result = null;

        ListNode node = head;
        while (node != null) {
            result = new ListNode(node.val);
            result.next = lastSeenNode;
            lastSeenNode = result;
            node = node.next;
        }

        return result;
    }
}
```
