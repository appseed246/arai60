# step4 レビューを受けて解答を修正
- Google Java Style Guide に準拠するように、エディタのインデントサイズをスペース4つから2つに変更。
- `parenthesis` は 丸括弧を意味するため、コード全体で `bracket` に変更
- スタックに入っている文字種が変数名で明確になるため、 `stack` から `openBrackets`に変更
- より簡潔に記述ができるため、`.keySet().contains()` を `.containsKey()` に変更。
  - ここは 閉じカッコをキー、開カッコを値としたMapにして `.contains` を使用するでも良さそう。
  - 今回は、カッコの対応関係として、「開カッコに対して閉じカッコが対応する」という順番が自然と判断して `openToClose`なMapで定義した。
- 閉じカッコの判定時に、期待する閉じカッコの文字を `char expectedCloseBracket = openToCloseBrackets.get(openBrackets.pop());` として変数化した。

```java
class Solution {
  private static Map<Character, Character> openToCloseBrackets = Map.of(
    '(', ')',
    '{', '}',
    '[', ']'
  );

  public boolean isValid(String s) {
    var openBrackets = new ArrayDeque<Character>();

    for (char bracket : s.toCharArray()) {
      if (openToCloseBrackets.containsKey(bracket)) {
        openBrackets.push(bracket);
        continue;
      }

      if (openBrackets.isEmpty()) {
        return false;
      }

      char expectedCloseBracket = openToCloseBrackets.get(openBrackets.pop());
      if (expectedCloseBracket != bracket) {
        return false;
      }
    }

    return openBrackets.isEmpty();
  }
}
```