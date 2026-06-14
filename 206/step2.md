# Step2 他の人のコードを読んで、コードを整える

## コードを整える
+ ロジックの入れ替えは特に必要なさそうに思えたのでなし。
  + 強いていえば、`node` 変数の必要性？ 個人的に 引数で渡された `head` を直接更新するのは避けたかったので `node` 変数を用意した。
+ 変数の命名
  + `lastSeenNode`の命名は気になる。 
    + 「1個前のノード」と「現在見ているノード」の二つを手元に持っておいて、後者 に 前者をつなげるというロジックで解いた
    + 「1個前のノード」の名前としてもう少しスマートな名前を付けられそう。
  + `result` という命名も、「最終的に返却する、反転したリストの先頭ノード」という意味ではもう少し良い命名がありそう。
    + -> 他人のコードを見た感じ、`reversed` とかついていると良さそうだった。
    + -> 反転した新しいリストの先頭なので、 `newHead` という命名もあった。
    + 構築済みのリストとかの表現もありそう。
  + 命名については、処理を手作業で実施することを考えたときに、作業を引き継ぐときにどんなメモを残せば後任の人は残りの作業をやりやすくなるかを考えるのが良いそう。

## 他の人のコードを読む
+ https://github.com/tamagoyaki-chiu/LeetCode-Practice/pull/12/changes
  + ノードは `new` しなくても、あらかじめ `node.next` を保持しておいて、最後に `node = next` で問題なかった。 `result` も不要になる。
    + ただし、最後に返却するのは `lastSeenNode` (PRでは `last_fixed_node`)。 node は ループ後にnullを参照している。
        ```java
        public ListNode reverseList(ListNode head) {
            ListNode lastSeenNode = null;
            ListNode node = head;

            while (node != null) {
                var next = node.next;
                node.next = lastSeenNode;
                lastSeenNode = node;
                node = next;
            }

            return lastSeenNode;
        }
        ```
  + 再帰での解法もあるので理解してみる。
    + (後述)
  + > 再帰関数は（最適化のない素朴な場合には）関数呼び出し自体がスタックに積まれていくのでスタックを使う処理と相性がいい、という理解の仕方もあるかもしれません。
    + 確かに。

### 再帰での解法

#### 解法a
```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null) {
            return null;
        }

        var newHead = head;
        if (head.next != null) {
            newHead = reverseList(head.next);
            head.next.next = head;
        }
        head.next = null;

        return newHead;
    }
}
```

#### 解法b
```java
class Solution {
    private ListNode reverseListHelper(ListNode reversedListHead, ListNode rest) {
        if (rest == null) {
            return reversedListHead;
        }

        var next = rest.next;
        rest.next = reversedListHead;
        return reverseListHelper(rest, next);
    }

    public ListNode reverseList(ListNode head) {
        return reverseListHelper(null, head);
    }
}
```

### 解法を理解する。
+ `1 -> 2 -> 3 -> null` の場合に、head = 2 の時、
  + reverseList(3) = 3 のため newHead = 3;
  + `head.next.next = head` -> `3.next = 2` となり、紐づけが反転される。
  + `head.next = null` は もともとの `2 -> 3` という紐づけを `2 -> null` にして、紐づけを消している。
  + 最後に新しい先頭ノード(newHead)の `3` を返却。
+ `newHead = reverseList(head.next)` で、`next`移行のリストを全部反転させてもらってから、最後に `head.next.next` で末尾の紐づけを反転させるイメージ。
+ リストの末尾までたどりついたら紐づけを反転して、反転済みのリストの先頭を返却してもらうイメージ。
  + 今見てるノードの次のノードから先は反転済み、最後に今見てるノードの紐づけを反転して、依頼主に提供する。
+ コードをみて理解はできたが、じゃあ空でこのロジックを思いつくには？がこの辺(↓)にかかれていそう。
  + https://discord.com/channels/1084280443945353267/1231966485610758196/1239417493211320382
    + > 私、これ結構はっきりしていると思っていて、「逆順になった部分の先頭と尻尾」じゃないですか? 先頭がないと次に返すのに困るし、尻尾がないと自分が付け足すのに全部辿らないといけません。
    + 尻尾に関しては、自分が担当するリストの先頭 `head` の次のノードだから、依頼先から明示的に教えてもらう必要はない
  + https://github.com/goto-untrapped/Arai60/pull/27#discussion_r1638693522
    + > 1. 頭から5番目までひっくり返した物を渡して、全部がひっくり返ったものを返してもらう。
    + > 2. 何も渡さずに、6番目以降をひっくり返したものを返してもらう。
      + 解法a は 2. に当たる(かな？)
      + 1. は自分で解いてみた。(前述: 解法b)
  + https://github.com/rinost081/LeetCode/pull/8#discussion_r1870989916
    + 解法a で `head.next` を `tail` とするとわかりやすい。

## コードを整える
ここまでの内容を踏まえて改めてコードを整えてみる。

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
        ListNode newHead = null;
        ListNode node = head;

        while (node != null) {
            ListNode rest = node.next;
            node.next = newHead;
            newHead = node;
            node = rest;
        }

        return newHead;
    }
}
```