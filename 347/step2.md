# Step2 他の人のコードを読んで、コードを整える

## コードを整える
とりあえず現在の解法でコードを整理
- 頻度を記録する部分は `merge` 関数で置き換え可能。ただし処理がかなり隠蔽されるので、ぱっと見のわかりやすさは元の方が良いように思う。
- 上位`k`個の要素は `stream` 操作に繋げられる。
  - sort後に `limit(k)` で上位`k`個の要素に絞り込み、`mapToInt`でエントリのキー値をプリミティブ型に変換する。

```java
class Solution {
  public int[] topKFrequent(int[] nums, int k) {
    var frequency = new HashMap<Integer, Integer>();

    for (int num : nums) {
      frequency.merge(num, 1, Integer::sum);
    }

    return frequency.entrySet()
        .stream()
        .sorted(Map.Entry.comparingByValue(Comparator.reverseOrder()))
        .limit(k)
        .mapToInt(entry -> entry.getKey().intValue())
        .toArray();
  }
}
```

すべてstreamにチェインするなら以下
- `Collectors.groupingBy(Function, Collector)`
  - 分類関数 Function によって要素をグループ化したあと、指定された Collector で特定のキーに関連付けられた値の操作を実行する。
  - Function が Function.identity() なので、int を受け取ってそのまま int の値を返す。要は `(x) -> {return x}`
- `Collectors.summingInt(num -> 1)`
  - 入力要素に対して関数を適用した結果の合計を生成するCollectorを返す
  - 値が来たら入力値にかかわらず1を返す。[4, 4, 4] ときたら [1, 1, 1]になってこの配列の要素を合算するイメージ。

```java
class Solution {
  public int[] topKFrequent(int[] nums, int k) {
    return Arrays.stream(nums)
        .boxed()
        .collect(Collectors.groupingBy(
          Function.identity(),
          Collectors.summingInt(num -> 1)
        ))
        .entrySet()
        .stream()
        .sorted(Map.Entry.comparingByValue(
          Comparator.reverseOrder()
        ))
        .limit(k)
        .mapToInt(Map.Entry::getKey)
        .toArray();
  }
}
```

TreeMap のコンストラクタにComparatorを渡して値の降順でソートすることもできそう。

## 他の人のコードを読む
- https://github.com/hiroki-horiguchi-dev/leetcode/pull/9
  - 1つめの解法は自分の解き方に近そう。
  - `frequency`の格納は `getOrDefault` で書くと直感的で良いと思った。
    - `frequency.put(num, frequency.getOrDefault(num, 0) + 1);`
  - バケットソートについてわすれていたので調べる。
  - 計算量
    - 時間計算量 -> O(n + m + n + k) で O(n) になる
      - 頻度集計: O(n) (同じ)
      - m個の値をバケットに配置: O(m)
      - 最大n個のバケットを逆走査: O(n)
      - 結果作成: O(k)
    - 空間計算量 O(n + m + m + k) = O(n)
      - HashMap: O(n)
      - バケット配列: O(n)
      - 各値の格納: O(m)
      - 結果: O(k)
  - ヒープを使った解法
    - Javaのヒープ -> PriorityQueue を使用する
    - 最小値が先頭にくるHeapをつくり、要素kを超えた時点でヒープから値を取り出すことで、上位k個だけがヒープに残存するので、残存した要素を配列として返せばいい。
      - maxHeap より minHeap の方がヒープに入る最大要素がk個になりメモリ節約できる？
      - 今回は結果で返す配列の要素の順序は順不同だが、頻度が高い順などの指定がある場合は結果を格納する配列をreverseしないといけないので注意が必要。
    - L71: HashMapのエントリを拡張forで回しているが、変数名は `map` より `entry` の方が好み
    - minHeap よりも kTops の方が良いという指摘 -> 構造の名前よりも中に何が入っているかを表す名前を付ける。
    - 計算量
      - 時間計算量 -> O(n + mlogk)
        - HashMap用意: O(n)
        - ヒープの構築: O(mlogk) -> 最も大きい
          - add() -> O(logk), poll() -> O(logk)で m 個処理する
        - 結果の取り出し: O(klogk)
          - poll()操作 logk を k回実行
      - 空間計算量 -> O(m + k)
        - HashMap: O(m)
        - ヒープ: O(k)
- コメント集
  - Rubyだとtallyというメソッドで頻度を計算できる
  - QuickSelect というアルゴリズムがあるらしい。
    - ★ 要調査
  - 

## 補足: バケットソート
整列の対象となっている整数の範囲が事前にわかっていて、整数の範囲があまり大きくない場合に高速にソート可能。
- 1~mまでの番号がついたバケツを準備 -> O(m)
- n個の要素を1~mまでのバケツに挿入する -> O(n)
- 1~mまでのバケツから要素を取り出す -> O(m+n)

時間計算量・空間計算量ともに O(m+n)。

