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
  - OpenJDK 25だっと(2025/11/14現在)
    - https://support.leetcode.com/hc/en-us/articles/360011833974-What-are-the-environments-for-the-programming-languages
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

### HashSet.contains(o)の時間計算量が`O(1)`である理由

#### 一般的なハッシュテーブルの仕組み
- ハッシュテーブルへの追加
  - ハッシュコードの計算: ハッシュ関数でハッシュコードを計算する
    - Javaだとオブジェクトの`hashCode()`が呼び出される
      - https://docs.oracle.com/javase/jp/8/docs/api/java/lang/Object.html#hashCode--
      - `public int hashCode()`
  - インデックスの決定: `ハッシュコード % バケット配列のサイズ` を計算して、バケットのどの位置にオブジェクトを格納するかを決定する。
  - バケットへの格納: インデックスの示す位置にオブジェクトを格納する。ハッシュが衝突した場合は、バケット内部の線形リストに要素を追加する。
- ハッシュテーブルに要素が含まれるかの判定
  - ハッシュコードの計算: ハッシュ関数でハッシュコードを計算する。
  - インデックスの決定: ハッシュコードから、オブジェクトが格納されている可能性のあるバケットのインデックスを決定する
  - バケット内の検索: インデックスからバケットにアクセス
    - 衝突がない場合: バケット内に目的のオブジェクトがあれば検索完了
    - 衝突がある場合: バケット内のリストを線形に走査して、目的のオブジェクトと一致するか順番に検証する。
- 時間計算量が`O(1)`である理由
  - ハッシュ関数が均等にハッシュコードを分散させて、ほぼ衝突がない理想的な状況では、時間計算量は`O(1)`となる
    - ハッシュコードの計算は定数時間
    - バケット配列へのアクセスは定数時間
    - バケット内部にオブジェクトが存在するかの確認は、定数時間(衝突がない場合)。
  - ハッシュが多発する最悪のケースでは、バケット内部のリストを順番に走査する必要があるため、`O(n)`となる可能性あり。

#### JavaのHashSet
- https://github.com/openjdk/jdk25u/blob/master/src/java.base/share/classes/java/util/HashSet.java
- HashSetは内部でHashMapを保持している。
- `public boolean add(E o)`
  - 引数のオブジェクトがSet内に存在しない場合はtrueを返す。
    - これを使ったコードも書けそうだが、`contains`のほうがわかりやすいだろう。
  - 内部では`map.put`を呼び出している。keyにオブジェクト、valueにダミーの`new Object`を渡している。
    - HashMap.putの本体は`putVal`という関数。
    - `final V putVal(int hash, K key, V value, boolean onlyIfAbsent, boolean evict)`
      - `int hash()`関数で、オブジェクトのhashCodeの上位16bitと下位16bitのXORを取ってハッシュコードを計算している。

## メモ
- denote: 示す