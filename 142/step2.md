# Step2 他の人のコードを読んで、コードを整える

## コードを整える
141の内容を踏まえて解答したため、特に整える部分はなし。

## 他の人のコードを読む
https://github.com/kt-from-j/leetcode/pull/2
ほぼ同じ内容。

- フロイドの循環検出法で改めて実装してみる。
  - サイクルの有無を検出はできるけど、どのnodeからサイクルが始まっているかはどうやってわかる？
    - slowとfastが衝突した地点と先頭からそれぞれ1ずつ進めて、両方のノードが重なったところがサイクルの開始地点となる。
- fastを中心に処理を記述。開始地点の探索時はslowにheadを代入して、それぞれのnodeを1ずつ進める。
- 計算量
  - 時間計算量: O(n) (循環検出: O(n), 開始地点探索: O(n)。2つの操作を順番に実施 → O(2n) → O(n)(係数2は無視される))
  - 空間計算量: O(1)
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
        // フロイドの循環検出アルゴリズムで探索する

        // 循環検出
        ListNode fast = head;
        ListNode slow = head;
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
            if (fast == slow) {
                break;
            }
        }
        if (fast == null || fast.next == null) {
            return null;
        }

        // 開始地点探索
        slow = head;
        while (fast != slow) {
            fast = fast.next;
            slow = slow.next;
        }

        return fast;
    }
}
```