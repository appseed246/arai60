# Step1 何も見ずに解く

- 今度は値が重複していないノードの連結リストを返却する。
- 値の重複は
- 思いついた解法
  - 解法(1)
    - 重複なしリストの入れ物を用意しておく。
    - ノードを先頭から順にたどって以下の操作を行う。
      - 現在のノードから、ノードの値が異なるノードまでたどる。もしくはノードがなくなったら終了。その時辿ったノードの数を覚えておく。
      - 辿ったノードの数が1個だけなら、現在のノードは重複なしと判断。重複なしリストに現在のノードを追加。
      - 辿ったノードの数が2個以上なら、現在のノードは捨てる。
      - たどり着いたノードから再度同じ操作を繰り返す。
    - ノードが存在しなくなるまでこれを繰り返す。
    - 計算量
      - 時間計算量: O(n)
      - 空間計算量: O(1)
        - 辿ったノードの数を保持する変数 + 重複なしリストを格納する変数
  - 解法2(解法誤り)
    - ノードを先頭からたどる。
    - ノードの値をHashSetに入れていき、参照できるノードがなくなるまで繰り返す。
    - HashSetの値を昇順に取り出して、ノードを連結する。
    - 計算量
      - 時間計算量: O(n)
        - リストを先頭から末尾まで参照 -> n
        - HashSetの要素を順番に参照 -> m
        - m <= n となるので最大でも2n -> オーダー的にはO(n) 
      - 空間計算量: O(n)
        - 最大サイズnのHashSet
  - 解法3
    - ノードのvalの値ごとに、ノードがいくつあるかを記録する変数を用意。
      - `HashMap<Integer, Integer>`で用意する。key = val, value = 値がvalであるノードの数。
    - ノードを先頭からたどる
    - ノードの値をキーとして、HashMapを参照する。キーが存在しないなら「1」を設定。キーが存在するなら1増加する。
    - HashMapをキーの値の昇順に順番に参照する。valueの値が「1」である場合、keyの値をListNodeのvalとしてインスタンス生成して、リストとして連結する。
    - 上記の操作でできた連結リストを返却する。
    - 計算量
      - 時間計算量: O(n)
        - ノードを先頭からたどる操作 -> O(n)
        - HashMapをもとにリストを生成する操作 -> O(n)
      - 空間計算量: O(n)
        - HashMapのサイズ: m (m <= n)
- 解法の比較
  - (1)はもとのリストを壊さないが、(2)はノードを最初から作り直す。
  - 空間計算量で(2)は(1)に劣る。だが、入力のリストの最大長が300なのでこのケースにおいては性能差はそれほどない。
    - 空間計算量の差は、具体的にはNodeListが (Integer型の変数のサイズ + ListNodeのポインタ型のサイズ)で計算できそう。
  - ~~手順的に(2)のほうが少ない手順でいくことにした。~~
    - 解法があやまりだったので、後で解法3でいくことにした。

- 通常のHashSetはtoArrayしたときの順序が定められていないため、TreeSetを使用する
- 何度かコンパイルエラー
  - `sorted = null`の後にカンマ忘れ
  - javaのforeachは`foreach`ではなく`for`
  - Setの型ミス。ListNodeではなくinteger
- コンパイルエラーはとったがWA．そもそもノードの値をすべてSetに入れると重複しているノードの値も入ってしまうことに気がつく。
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

        Set<Integer> set = new TreeSet<Integer>();
        while (head.next != null) {
            set.add(head.val);
            head = head.next;
        }

        ListNode sorted = null;
        for (int value: set) {
            if (sorted == null) {
                sorted = new ListNode(value);
            } else {
                sorted.next = new ListNode(value);
                sorted = sorted.next;
            }
        }

        return sorted;
    }
}
```

- 解法3を記述。
- HashMap周りの操作がわからず調査。
  - keyのソート: keySet()を取得してArrayListに変換後、Collections.sort()
- 初回提出: WA。
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

        Map<Integer, Integer> nodeCount = new HashMap<Integer, Integer>();
        while (head != null) {
            if (!nodeCount.containsKey(head.val)) {
                nodeCount.put(head.val, 1);
            } else {
                int currentValue = nodeCount.get(head.val);
                nodeCount.put(head.val, currentValue + 1);
            }
            head = head.next;
        }

        List<Integer> keys = new ArrayList(nodeCount.keySet());
        Collections.sort(keys);

        ListNode sorted = null;
        for (int key: keys) {
            if (nodeCount.get(key) == 1) {
                if (sorted == null) {
                    sorted = new ListNode(key);
                } else {
                    sorted.next = new ListNode(key);
                    sorted = sorted.next;
                }
            }
        }

        return sorted;
    }
}
```

- returnで返しているのが、生成したリストの最後のノードであることに気づいたので修正して、AC。
- 解法の検討からACまでに1時間15分かかった。長い。
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

        Map<Integer, Integer> nodeCount = new HashMap<Integer, Integer>();
        while (head != null) {
            if (!nodeCount.containsKey(head.val)) {
                nodeCount.put(head.val, 1);
            } else {
                int currentValue = nodeCount.get(head.val);
                nodeCount.put(head.val, currentValue + 1);
            }
            head = head.next;
        }

        List<Integer> keys = new ArrayList(nodeCount.keySet());
        Collections.sort(keys);

        ListNode sorted = null;
        ListNode sortedHead = null;
        for (int key: keys) {
            if (nodeCount.get(key) == 1) {
                if (sorted == null) {
                    sortedHead = new ListNode(key);
                    sorted = sortedHead;
                } else {
                    sorted.next = new ListNode(key);
                    sorted = sorted.next;
                }
            }
        }

        return sortedHead;
    }
}
```

- 解法3の実装は、HashSet周りの関数の知識が不足していて、調査に時間がかかってしまった。
- 解法3はreturnの値の返却させなおせばACだったので、事前に検討した解法手順をコードに落とし込む精度は、前回ACになるまで修正を3回ほど繰り返したときよりはマシになっていそう。
  - 前回: https://github.com/appseed246/arai60/pull/4/files#diff-07d23b41102b369110e34a980e11322f8db73c4d94125d092fbd693dd56e8eb4