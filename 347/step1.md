# Step1 何も見ずに解く
- 考えたこと
  - 配列が渡されて、出現する回数の上位 `k` 番目までの数をすべて返却する
  - カテゴリがヒープだからヒープを使うんだろうと思ってしまったが、空で解法を考えてみる。
  - 思いついた解法
    - 出現する値をキーとして、出現回数を値に取るマップを用意する。
    - 配列を頭から舐めてマップに出現回数を記録する。
    - 出現回数を記録したあと、マップのエントリを値の大きい順番にソートして、上位 `k` 番目のキーを配列に格納して返却する
    - 時間計算量: ~~O(n+k)~~ -> O(mlogm)
      - 頻度を調べるために要素 `n` 個を走査 -> n
      - 上位 `k` 個の要素を取得するために`k`個を走査 -> k
      - ソートの計算量をどう扱うかが正確に把握できておらず。
        - Strem<T>では Timソート、IntStream,LongStream,DoubleStream では Dual-Pivot QuickSort。
          - 内部の end 関数で `Array.sort` でソート処理を実行。Array.sort() は `Timsort.sort()` に移譲される。
            - TimSort は 計算量 `O(mlogm)`。 (m = nums内の異なる値の数)
    - 空間計算量: ~~O(n+k)~~ -> O(n)
      - HashMapの分が`n` (想定)
        - 実際どう計算されるかは正確に把握できていない。
          - キーの数 ~~`n`~~ `m` に対して値を持つので、~~`O(n)`~~ `O(m)`
      - 上位`k`個の要素の配列分が`k`
      - ソートしたマップエントリの配列はどう扱う？
        - HashMapと別に `m`個のエントリを持つ -> `O(m)`
  - ここまで 7:20秒
  - 実装
    - Mapのエントリを逆順にソートする方法がわからず調べる
    - 最初 Map.Entryの配列を `toArray` で配列に変換したが参照時に `getKey`が使用できなかったため、`toList`でリストにするように変更。
    - 27:49 で Accept。

```java
class Solution {
  public int[] topKFrequent(int[] nums, int k) {
    var frequency = new HashMap<Integer, Integer>();

    for (int num : nums) {
      if (frequency.containsKey(num)) {
        frequency.put(num, frequency.get(num) + 1);
      } else {
        frequency.put(num, 1);
      }
    }

    int[] topKElements = new int[k];
    List<Map.Entry<Integer, Integer>> sorted = frequency.entrySet()
      .stream()
      .sorted(Map.Entry.comparingByValue(Comparator.reverseOrder()))
      .toList();
    for (int i = 0; i < k; i++) {
      topKElements[i] = sorted.get(i).getKey();
    }

    return topKElements;
  }
}
```

## 参考
+ Arrays.java
  + https://github.com/openjdk/jdk/blob/master/src/java.base/share/classes/java/util/Arrays.java
  + L88-L100: int[] に対して、`DualPivotQUicksort.sort(a, 0, 0, a.length)` を実行
  + L1298-L1305: Object[] に対して、`Timsort.sort(a, fromIndex, toIndex, c, null, 0, 0)` を実行
+ 