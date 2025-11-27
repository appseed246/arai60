# Step1 何も見ずに解く
```
Given the `head` of a sorted linked list, delete all duplicates such that each element appears only once. Return the linked list sorted as well.
```

## 解答
- ソート済みの連結リストから重複するノードを削除する。関数の返却値は連結リストの先頭のノードで良い？
- とりあえず`head`がnullでないことはチェックしよう
  - whileの条件文で弾けたので、コード量を減らすため、明示的に`head == null`のガードは書かなかった。
- ソート済みなので、値の重複は前のノードの値を保持する変数を用意して、現在のノードの値と同じならば現在のノードを切り離せば良さそう。
- `previeous`の初期値には入力値の範囲外の値をセットする。
  - 入力値は-100 ~ 100の間なので、この範囲外のINTの最大値を入れておこう。
    - INT_MAXでいいんだっけ。
  - int型の変数の`previousValue`として用意しようかと思ったが、連結リストの重複検出時にノードの付け替えができないと思ったので、ListNode型の変数`previous`を用意した。nullチェックを省けるように`new ListNode(INT_MAX)`を設定。
- 提出: エラー。INT_MAXという変数が存在しない
  - intの最大値は正しくは`Integer.MAX_VALUE`だった。
    - https://docs.oracle.com/en/java/javase/25/docs/api//java.base/java/lang/Integer.html#MAX_VALUE
- 解答時間: 16:03
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
        ListNode previous = new ListNode(INT_MAX);
        ListNode current = head;

        while (current != null) {
            if (current.val == previous.val) {
                previous.next = current.next;
            }
            previous = current;
            current = current.next;
        }

        return head;
    }
}
```

- `INT_MAX`を`Integer.MAX_VALUE`に変更して提出 → エラー
- 重複を削除しきれていないようだった。
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
        ListNode previous = new ListNode(Integer.MAX_VALUE);
        ListNode current = head;

        while (current != null) {
            if (current.val == previous.val) {
                previous.next = current.next;
            }
            previous = current;
            current = current.next;
        }

        return head;
    }
}
```

- WAの入力値`[1,1,1]`で脳内シミュレートしてデバッグした。if文の中でpreviousにcurrentの次のノードをつなげたあと、previousとcurrentを一つづつずらすと、重複を削除しきれていないようだったので、`continue`を追加。
- 結果、Time Limit Exceeded
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
        ListNode previous = new ListNode(Integer.MAX_VALUE);
        ListNode current = head;

        while (current != null) {
            if (current.val == previous.val) {
                previous.next = current.next;
                continue;
            }
            previous = current;
            current = current.next;
        }

        return head;
    }
}
```

- 再度動作をシミュレーション。previous.nextをcurrent.nextに差し替えたあと、currentが切り離したノードを指したままであったことに気づく。currentはcurrent.nextに差し替えて次のループで比較しないといけなさそう。
- 提出 → Accepted.
- 最初の解答から13分後に提出。
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
        ListNode previous = new ListNode(Integer.MAX_VALUE);
        ListNode current = head;

        while (current != null) {
            if (current.val == previous.val) {
                previous.next = current.next;
                current = current.next;
                continue;
            }
            previous = current;
            current = current.next;
        }

        return head;
    }
}
```