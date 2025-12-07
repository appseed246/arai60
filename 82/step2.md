# Step2 他の人のコードを読んで、コードを整える

## コードを整える
- TreeMapを使用すると、keyの昇順にエントリを回すのが楽。
  - https://docs.oracle.com/javase/jp/8/docs/api/java/util/TreeMap.html
  - `entrySet()`を取得し、foreach文で回す。key,valueそれぞれ`getKey()`,`getValue()`で取得。
    - `map.foreach(() -> {})`でラムダ式を使う方法もあるが、ラムダ式は式外の変数の書き換えができない。今回は`sorted`を書き換えるため、不採用。
- keyがないなら1, keyがあるなら加算の処理のリファクタ
  - `merge`関数を使用する方法もある。
    - ```
      nodeCount.merge(head.val, 1, Integer::sum);
      ```
  - `getOrDefault`でデフォルト値を0にしてもよい。

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

        Map<Integer, Integer> nodeCount = new TreeMap<Integer, Integer>();
        while (head != null) {
            nodeCount.merge(head.val, 1, Integer::sum);
            head = head.next;
        }

        ListNode sorted = null;
        ListNode sortedHead = null;
        for (Map.Entry<Integer, Integer> entry: nodeCount.entrySet()) {
            int val = entry.getKey();
            int count = entry.getValue();
            if (count != 1) {
                continue;
            }

            if (sorted == null) {
                sorted = new ListNode(val);
                sortedHead = sorted;
            } else {
                sorted.next = new ListNode(val);
                sorted = sorted.next;
            }
        }
        
        return sortedHead;
    }
}
```

## 他の人のコードを読む
- https://github.com/hiroki-horiguchi-dev/leetcode/pull/4
  - 値が連続している区間をすっとばして最初に違う値がきたら、tailの末尾に張り替える方法。
  - 自分が考えた解法(1)に近いが、これだといくつ辿ったかを覚えておく必要がないのでスマート。
  - nullのケースを避けるためにdummyを用意しているのが良い。
  - コメントみて、dummyの初期値が-1で入力値の範囲内であることに気が付かなかった。
- https://github.com/kt-from-j/leetcode/pull/4
  - 破壊・非破壊については自分も気にしていたところで、同様にコメントが書かれていた。
  - 自分の解答でheadの変数を破壊していたので、別の変数に詰め直すようにして修正する
  - スタックを使用した実装があった。(推奨はArrayDeque)
    - スタックからpeekして、値が同じならスタックからpopすることで重複のあるノードをすべて排除している。
    - 重複の検出をどうすべきか迷っていたので参考になる。
  - 後ろからノードを連結する方法として`ListNode(val, tail)`があった。参考になる。
  - val よりの nodeValのほうがよいかも。

## 他回答
- 重複のないノードが連結されているリストを保持する`tail`を用意する。tailにはダミーノードを格納する。
- リストを頭から走査する。
- 現在のノードと次のノードが違う値なら、現在のノードをtailに連結する。連結したノードをtailとして扱う。連結後、次のノードを参照。
- 現在のノードと次のノードが同じ値なら、次のノードの値が異なるノードまで進める。
- 参照できるノードがなくなるまで走査を続ける。走査が終わったらtail.nextを返却する。

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

        // 初期値にはダミーのノードを設定する
        ListNode newHead = new ListNode();
        ListNode tail = newHead;
        ListNode node = head;
        while (node != null) {
            if (node.next == null || node.val != node.next.val) {
                tail.next = new ListNode(node.val);
                tail = tail.next;
                node = node.next;
            } else {
                while (node.next != null && node.val == node.next.val) {
                    node.next = node.next.next;
                    // node = node.next;
                }
                node = node.next;
            }
        }

        return newHead.next;
    }
}
```
- `tail.next = node`ではダメで`tail.next = new ListNode(node.val)`でないと行けない。
  - `node`をつなげると、`node`以降に連結されているノードも一緒に連結されてしまうため。

## ArrayDequeの解答

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

        ArrayDeque<Integer> nonDuplicateValues = new ArrayDeque();
        ListNode node = head;
        while (node != null) {
            if (nonDuplicateValues.isEmpty() || nonDuplicateValues.peekLast() != node.val) {
                nonDuplicateValues.add(node.val);
            } else {
                nonDuplicateValues.pollLast();
                while (node.next != null && node.val == node.next.val) {
                    node = node.next;
                }
            }
            node = node.next;
        }

        // 初期値にはダミーの値を設定する。
        ListNode newHead = new ListNode();
        ListNode tail = newHead;
        Integer popValue = null;
        while((popValue = nonDuplicateValues.poll()) != null) {             
            tail.next = new ListNode(popValue);
            tail = tail.next;
        }

        return newHead.next;
    }
}
```