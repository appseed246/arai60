# Step2 他の人のコードを読んで、コードを整える

## コードを整える
- ノードの順序性はprevious -> currentという順番なので、条件や変数の代入はこの順番で記述するのがよさそう。
  - この順序性に従って、while内のif文の条件を`previous.val == current.val`(previous -> currentの順)に修正。
- if - continueのパターンは if - elseに書き換え可能
  - 今回はCの処理は短いのでif - elseで書くのがよいかも。入れ替え処理のインデントも揃う。
- previousの初期値
  - リストはノードの値が昇順にならんでいるので、previousの初期値は`Integer.INT_MIN`のほうが自然か？
```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        // ダミーのノードを初期値に設定する。
        // valは入力値の範囲外の値を設定する。
        ListNode previous = new ListNode(Integer.MIN_VALUE);
        ListNode current = head;

        while (current != null) {
            if (previous.val == current.val) {
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

- もしvalの範囲がintやlong全体だったらpreviousの初期値ノードのvalの値は？
  - previous自体をnullにして、条件文を`if (previous != null && previous.val == current.val)`にするしかなさそう。
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
        // ダミーのノードを初期値に設定する。
        // valは入力値の範囲外の値を設定する。
        ListNode previous = null;
        ListNode current = head;

        while (current != null) {
            if (previous != null && previous.val == current.val) {
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

- previousなしで記述可能か？
  - 変数1つで行けた。操作の記述としても他人にもわかりやすいかなと思う。
    - 「ノードの値と次のノードの値が一致するなら、次のノードは次の次のノードに付け替える。値が一致しない場合は次のノードに進む。」という処理は、自分の感覚だと、他人からも自然に読み取れるように思えるがどうだろうか。
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
                node.next = node.next.next;
            } else {
                node = node.next;
            }
        }

        return head;
    }
}
```

## 他の人のコードを読む

## 一人目
https://github.com/hiroki-horiguchi-dev/leetcode/pull/3
- 計算量を考えるのを忘れていた。
  - 時間計算量: O(n)
  - 空間計算量: O(1)
- 別にわざわざpreviousを用意せずともcurrent, nextの組み合わせで良かったことに気づく。
  - 自分の考えとしては、先頭のnodeをcurrentとして処理を考えたかった。currentが最後に到達した時点で処理終了という考え。
- current, next版の処理。若干書き味が変わる感覚が合ったが、脳内シミュレーションしながら一発で通せた。
- 変数のnextとフィールドのnextが同じなので、若干読みづらく感じる。currentNode, nextNodeとしてもいいが、冗長な気もする。
- headがnullのケースは、明示的に`return null`と記述したほうが変える値が明確なのでよいかも。
  - いやだめ、`head != null && head.next == null`のケースでWAになる。
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
        if (head == null || head.next == null) {
            return head;
        } 
        ListNode current = head;
        ListNode next = head.next;

        while (current != null && next != null) {
            if (current.val == next.val) {
                current.next = next.next;
                next = next.next;
            } else {
                current = next;
                next = next.next;
            }
        }

        return head;
    }
}
```

- この方の解答では重複削除時に、currentを固定して、重複しなくなるまでnextを進めているやりかただった。
  - whileループが2重になるので冗長に感じた。シングルループのほうが可読性はよいと思う。
- コメント参照
  - nextという変数名は隣接を表すのでforwardが良い: 自分の処理ではcurrent, nextは常に隣接関係にあるからnextでも問題なさそう。ただnext.nextがわかりづらい問題は残る。
  - 変数nextのスコープが広い: 自分の解答ではスコープは狭められない？ただ、`next = next.next`はif-elseの両方に存在しているので、括り出してよさそう。

- 書き換えたwhile部分を抜粋。`next = next.next`が浮いている感じがするので微妙。
```java
while (current != null && next != null) {
    if (current.val == next.val) {
        current.next = next.next;
    } else {
        current = next;
    }

    next = next.next;
}
```

## 二人目
https://github.com/kt-from-j/leetcode/pull/3

- 一人目の方の処理が自分の方針とだいぶ異なっていたので、もう一人見てみることにした。
- この方も自分と異なっていた。HashSetを使った解法。
  - 自分も最初HashSetを使う解法もあるかもとうっすらと考えていたが、空間計算量の面で有利な解法を思いついていたので、HashSetを使った解法は詳細に考えきっていなかった。
  - 正直な感想としては、この問題で他の方の解法の方針が自分の解法の方針とだいぶ異なるとは思っていなかった。
  - 自分は時間計算量や空間計算量が大きなオーダーのアルゴリズムでないものならすぐに実装に取り掛かっていた。もう少し他の選択肢を脳内で検討したうえで実装するとよいかもしれない
    - コメント集にあった、Aが動くからAで実装するのではなく、A,B,Cの選択肢を比較検討した上でAで実装する、というやつ。