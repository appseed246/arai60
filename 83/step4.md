# step4 レビューを受けて解答を修正

「手作業でどうやるかを考え、考えた手順をコードに落とし込む」ために、最初の解法を文書化してから解いてみた。
まず、最初の解法を文書化してからコードを書いてみたが、`previous - current`で考えるとコードを整えても違和感が消えなかった。`current - next`で考えるほうが自然に感じた。

----------

解法を文書化する。↓
----------

- ノードを先頭からたどって、以下の手順を繰り返す。現在のノードがなくなったら手順を止める。
  1. 現在のノードの値と直前のノードの値を比較する。直前のノードが存在しなければ、値の比較ができないので、現在のノードを直前のノードとして保持して、次のノードを見る。
  2.  現在のノードと直前のノードの値が同じなら、直前のノードに現在のノードの次のノードをつなげてから、つなげたノードを見る。値が異なる場合、現在のノードを直前のノードとして保持しておき、次のノードを見る。
- 上記手順実施後、先頭のノードを返却する。

----------

この手順をもとに再度実装してみる。
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
        ListNode previous = null;
        ListNode current = head;

        while (current != null) {
            if (previous == null) {
                previous = current;
                current = current.next;
            } else if (previous.val == current.val) {
                previous.next = current.next;
                current = current.next;
            } else {
                previous = current;
                current = current.next;
            }
        }

        return head;
    }
}
```

コードを整える。`if (previous == null)`と`else`節の内容が同じなので、一つの条件にまとめたが、
「同じ値のノードを取り除く」という操作がメインのはずだが`else`節で記述されてしまっていてキモチワルい。
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
        ListNode previous = null;
        ListNode current = head;

        while (current != null) {
            if (previous == null || previous.val != current.val) {
                previous = current;
                current = current.next;
            } else {
                previous.next = current.next;
                current = current.next;
            }
        }

        return head;
    }
}
```

直前のノードが存在しないケースは別のif文で括りだしたほうが、
例外ケースのガードと値の比較処理というメインの判定を行うという表現になる。
ただ、例外ケースは最初の一回だけなので、ループで毎回nullチェックが走るのは無駄な感じ。
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
        ListNode previous = null;
        ListNode current = head;

        while (current != null) {
            if (previous == null) {
                previous = current;
                current = previous.next;
                continue;
            }
            
            if (previous.val == current.val) {
                previous.next = current.next;
                current = current.next;
            } else {
                previous = current;
                current = previous.next;
            }
        }

        return head;
    }
}
```

`current`と`next`で考えてみる↓

解法の文書化

-------
- 現在のノードと次のノードの値を比較する。
  1. 現在のノードと次のノードの値が同じ場合、次のノードは削除対象。現在のノードに次の次のノードをつなげる。
  2. 現在のノードと次のノードの値が異なる場合、次のノードが削除不要。次のノードを見る。
  3. この操作を続けて、次のノードが存在しなければ操作を止める。
- 操作が完了したあと、先頭のノードを返却する
-------

実装する。

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