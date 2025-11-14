# Step2 他の人のコードを読んで、コードを整える

## コードを整える
- 他の人のコードをみる手順があることに後で気づいたため、先にコードを整えている。
- HashSetの変数名を`visited`にして、意図を明確にした。
- 1度目の提出でRunTimeエラーになった。headのnullチェックを忘れていたので、追加。
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
    public boolean hasCycle(ListNode head) {
        if (head == null) {
            return false;
        }
        ListNode node = head;
        Set<ListNode> visited = new HashSet<ListNode>();
        visited.add(node);
        while(node.next != null) {
            node = node.next;
            if (visited.contains(node)) {
                return true;
            }
            visited.add(node);
        }
        return false;
    }
}
```
- 他のパターン
  - 新しいインスタンスを生成し、`next`に`head`を設定し、最初の`node`とすることでnullチェックが不要になる。
    - 説明なしに新しいインスタンスを生成していて、意図が伝わりにくいので、あまりいい方法ではないかもしれない。素直でnullチェックするで良さそう。
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
    public boolean hasCycle(ListNode head) {
        ListNode node = new ListNode(0, head);
        Set<ListNode> visited = new HashSet<ListNode>();
        visited.add(node);
        while(node.next != null) {
            node = node.next;
            if (visited.contains(node)) {
                return true;
            }
            visited.add(node);
        }
        return false;
    }
}
```
関数の先頭のnullチェックが不要なパターンならこちらでもいい。
ただ、先頭にnullチェックがあったほうが、レビュワーに対してnullを確実に弾いていることを一目でわかるようにできるメリットはある。
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
    public boolean hasCycle(ListNode head) {
        Set<ListNode> visited = new HashSet<ListNode>();
        ListNode node = head;
        while(node != null) {
            if (visited.contains(node)) {
                return true;
            }
            visited.add(node);
            node = node.next;
        }
        return false;
    }
}
```


## 他の人のコードを読む
https://github.com/kt-from-j/leetcode/pull/1
- 問題文の説明を理解するのに時間を要したり、メソッド名を空で書けないなど同じだった。
  - posが問題を解くのに不要な点など。
- nodeのnullチェックはwhileの条件としている
  - 不要ではという指摘があったので、素直に関数の先頭でnullチェックでよさそう。
- nullチェックのif分をブレースなしで書いていて、コードがコンパクトにまとまっている。
  - 個人的な好みとしてはブレースはかならずある方がすき。
  - コメント集でもぶらさがりif文について言及されていて、基本はブレースありを推奨のよう。
    - https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.gblcuhn8bpdi
- HashSetを`visitedNode`としていた。
  - Nodeであることは自明なので不要という意見があり、他の解答者もvisitedやreachedが多いとのこと
- **フロイドの循環検出法**
  - slowとfast2つのノードを用意して、slowは1ノードずつすめ、fastは2ノードずつ進める
  - fastがslowに追いついたら場合、サイクルが検出できる。
  - 計算量
    - 時間計算量: `O(n)`
    - 空間計算量: `O(1)`
  - コード
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
        public boolean hasCycle(ListNode head) {
            if (head == null) {
                return false;
            }
            ListNode slow = head;
            ListNode fast = head;
            while(fast != null && fast.next != null) {
                slow = slow.next;
                fast = fast.next.next;
                if (slow == fast) {
                    return true;
                }
            }
            return false;
        }
    }
    ```