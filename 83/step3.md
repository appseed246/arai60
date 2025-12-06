# Step3 3回続けて10分以内に書いてエラーを出さなければOKとする

- `node`を使用した解法がシンプルで好みなので、そちらで実装する。

## 1回目
- `;`を忘れてコンパイルエラー(`node.next = node.next.next`の部分)
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
    public ListNode deleteDuplicates(ListNode head) {
        ListNode node = head;

        while (node != null && node.next != null) {
            if (node.val == node.next.val) {
                node.next = node.next.next
            } else {
                node = node.next;
            }
        }

        return head;
    }
}
```

## 2, 3回目(AC)
- headのnullは関数の頭で弾いて、whileはnextがnullでないことだけ判定するようにした。
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
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode node = head;

        while (node.next != null) {
            if (node.val == node.next.val) {
                node.next = node.next.next;
            } else {
                node = node.next;
            }
        }

        return head;
    }
}
```

# 4回目(WA)
`;`が足りずコンパイルエラー(`return null`の部分)。細かい部分に気を配れていなかった。
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
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) {
            return null
        }
        ListNode node = head;

        while (node.next != null) {
            if (node.val == node.next.val) {
                node.next = node.next.next;
            } else {
                node = node.next;
            }
        }

        return head;
    }
}
```

# 5回目,6回目(AC),7回目(WA)
関数のreturnでnodeを返却してWA。
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
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode node = head;

        while (node.next != null) {
            if (node.val == node.next.val) {
                node.next = node.next.next;
            } else {
                node = node.next;
            }
        }

        return node;
    }
}
```

# 8回目,9回目,10回目(AC)
- ようやく3回連続AC。平均解答時間 約50秒ほど。
- 計算量
  - 時間計算量: O(n)
  - 空間計算量: O(1)
- ケアレスミスが多発してしまった。
  - セミコロン忘れが2回発生。普段IDEで即座にコンパイルエラーのフィードバックを受けるので、セミコロンのケアはあまりしっかりしていなかった。自分の傾向として、IDE補助なしだとこういうクセがでやすいようなので注意したい。
  - `return node`は、処理記述中にこの関数の要件を思考せずに記述してしまっていたため、惰性で実装せず、脳内で操作を考えながら実装するクセはかならず必要そう。
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
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode node = head;

        while (node.next != null) {
            if (node.val == node.next.val) {
                node.next = node.next.next;
            } else {
                node = node.next;
            }
        }

        return head;
    }
}
```
