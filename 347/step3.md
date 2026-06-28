# Step3 3回続けて10分以内に書いてエラーを出さなければOKとする

- PriorityQueueの実装で解答する。
- 3分ほどでAccept。

```java
class Solution {
  public int[] topKFrequent(int[] nums, int k) {
    var frequency = new HashMap<Integer, Integer>();

    for (int num : nums) {
      frequency.put(num, frequency.getOrDefault(num, 0) + 1);
    }

    PriorityQueue<Map.Entry<Integer, Integer>> topKElements = new PriorityQueue<>(Map.Entry.<Integer, Integer>comparingByValue());

    for (var entry : frequency.entrySet()) {
      topKElements.add(entry);
      if (topKElements.size() > k) {
        topKElements.poll();
      }
    }

    int[] result = new int[k];
    for (int i = 0; i < k; i++) {
      result[i] = topKElements.poll().getKey();
    }
    
    return result;
  }
}
```


## Comparatorの実装
- 「負なら左が先、正なら右が先」
  - (a, b) -> Integer.compare(a, b) => a - b // 昇順
  - (a, b) -> Integer.compare(b, a) => b - a // 降順
  - Map.Entry の場合 `Map.Entry.<Integer, Integer>.comparingByValue()`でOK

## PriorityQueueの操作
- add(E e): 要素の追加
- peek(): 先頭の要素を取得。削除はしない。
- poll(): 先頭の要素を取得・削除する。