# Step2 他の人のコードを読んで、コードを整える

## コードを整える
あきらかに汚いので他人のコードを読む前に自分のコードを整える。
- ArrayList を ArrayDeque に変更
- poped は よく考えたら参照しているだけだから peeked に変更
- isOpenParenthsis → タイポ修正 (`isOpenParenthesis`)
- 開カッコが来たらケースはpushしてcontinueで早期リターン
- カッコの順序・種類が正しいことを判定するprivate関数 `isCorrectPair` の新設
  - `record` で ペアを表現するクラスを定義
  - カッコの種類毎に正しい組み合わせのペアを用意してSetに格納しておく。
  - 引数で渡された2つのカッコの文字をペアとしてSetに渡して、Setにペアが含まれることを判定する。
  - 「順序が正しい」と「種類が同じ」を判定する関数の名前として `Correct` を使用したが、もっといい命名はありそう。
  - 正しいペアの定義は`private static`としてクラスのフィールドに定義でもいいかも。
```java
class Solution {
    public boolean isValid(String s) {
        var stack = new ArrayDeque<Character>();

        for (char parenthesis : s.toCharArray()) {
            if (isOpenParenthesis(parenthesis)) {
                stack.push(parenthesis);
                continue;
            }

            if (stack.size() < 1) {
                return false;
            }

            char poped = stack.pop();
            if (!isCorrectPair(poped, parenthesis)) {
                return false;   
            }
        }

        return stack.size() == 0;
    }

    private boolean isOpenParenthesis(char p) {
        return p == '(' || p == '{' || p == '[';
    }

    private boolean isCorrectPair(char openParenthesis, char closeParenthesis) {
        Set<Pair> parentheses = Set.of(
            new Pair('(', ')'),
            new Pair('{', '}'),
            new Pair('[', ']')
        );

        return parentheses.contains(new Pair(openParenthesis, closeParenthesis));
    }

    private record Pair(char left, char right) {}
}
```

### 参考
+ https://docs.oracle.com/javase/jp/8/docs/api/java/util/ArrayDeque.html
+ https://docs.oracle.com/javase/jp/21/docs/api/java.base/java/util/Set.html

## 他の人のコードを読む
https://github.com/HitoshiKoba/Arai60-public/pull/2/files

+ step2の解答は概ね自分の実装と同じ
+ オートボクシングは意識はしていた。
+ stack.size == 0, stack.size() < 1 は stack.isEmpty で 書き換えられる。
+ 開カッコの判定は `"([{".contains()` でも行ける
+ ペア判定は Set<Pair> でなくて Map でよさそう。

## 再度コードの整理
```java
class Solution {
    private static Map<Character, Character> openToCloseParentheses = Map.of(
        '(', ')',
        '{', '}',
        '[', ']'
    );

    public boolean isValid(String s) {
        var stack = new ArrayDeque<Character>();

        for (char parenthesis : s.toCharArray()) {
            if (openToCloseParentheses.keySet().contains(parenthesis)) {
                stack.push(parenthesis);
                continue;
            }

            if (stack.isEmpty()) {
                return false;
            }

            char poped = stack.pop();
            if (openToCloseParentheses.get(poped) != parenthesis) {
                return false;   
            }
        }

        return stack.isEmpty();
    }
}
```
