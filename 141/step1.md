# Step1 何も見ずに解く
- ListNodeのnextを辿って、サイクルになっている場合はtrueを返す
  - nextがnullならばそのノードが終端なのでfalse
  - サイクルの検知
    - nextを順に見ていく
    - valが同じnodeが存在する可能性があるため、valが同一であるかで判断は不可能？
  - Javaでインスタンスのアドレスを特定する方法がわからない -> Equalsで判断かのう？
  - とりあえず既存のノードをSetに入れて、includeされているかで判断
    - サイクルがない場合はwhileの条件でループが停止する、はず。
- そういえばLeetCodeのJavaバージョンはいくつ？
  - Java9移行だとSet.ofが使える
    - Set.ofはイミュータブルなコレクションを生成するため、今回のケースでは使用できなかった。
  - Setに含まれるかの関数は`includes()`ではなく`contains()`
- TestCase3でヌルポエラー
  - 原因はメモし忘れてしまった。
- Submit。headがnullのケースでエラーが発生したので、関数の先頭にnullチェックを追加。
- Accepted. 時間を測り忘れたが、30~40分くらいかかった。
- 計算量
  - 時間計算量: `O(n)`
    - headから順にN個のNodeを参照する。
    - Setのcontainsの判定は`O(1)`。Nodeの数(N個)だけ実行する。
      - ★ `O(1)`になる理由が思い出せないので後でSetのデータ構造を調べる
  - 空間計算量: `O(n)`
    - HashSetにN個のNodeが追加される
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
        ListNode node = head;
        Set<ListNode> nodes = new HashSet<ListNode>();
        nodes.add(node);
        while(node.next != null) {
            node = node.next;
            if (nodes.contains(node)) {
                return true;
            }
            nodes.add(node);
        }
        return false;
    }
}
```


## メモ
- denote: 示す