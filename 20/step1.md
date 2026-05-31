# Step1 何も見ずに解く
- 問題はすぐ理解。Exampleをみて問題の理解が合っているか確認した。
- スタック使って、ペアが見つかったらスタックからポップする方針でいけそう。最後にスタックの中身が空であればすべてカッコが正しくペアで存在していると判定できそう。
- コードを書き始めたが、まずスタックに適するデータ構造が何かわからなかった。とりあえずArrayListで解くことに。
- 覚えてなかったこと
  - foreach構文の書き方: `for (String element: collection)`
  - List に プリミティブ型の char を指定できない。する場合はボクシングされた型である `Character` を使用する。
  - リスト操作するための関数
    - 末尾の要素の参照: `get(stack.size() - 1)`
    - 末尾への要素の挿入: `add()`
    - 末尾の要素の削除: `remove(stack.size() - 1)`
    - リストの長さ: `size()` (lenght()ではない)
- もろもろ修正が必要だった。
  - セミコロン忘れ
  - リスト操作の関数の誤り。
  - poped と parenthesis が逆。
- 2回ほど手直ししてAccept。32分59秒で完了。
- 計算量
  - 時間計算量: `O(n)` (文字長分だけループ)
  - 空間計算量: `O(n)` (判定のためにスタックに文字を格納する分)
- 反省など
  - アルゴリズムのイメージはうっすらあったが、コードに落とし込むときにかなり試行錯誤してた。境界条件もあとから差し込む形でツギハギなコードになった。
  - if分のネストも深くあまりきれいな形ではない
  - 解答途中に、もしかしてArrayListは、末尾への要素の挿入・参照の計算量が `O(n)` で効率悪いのでは？と思う
    - 違った。それは連結リストで `LinkedList` の場合。
    - ArrayList では `get` は O(1)、 `add` は基本 O(1)、内部のバッファが枯渇した際は拡張されるため O(n)。
    - ただ、Javaでスタックを使うなら `ArrayDeque` が適切。

```java
class Solution {
    public boolean isValid(String s) {
        var stack = new ArrayList<Character>();

        for (char parenthesis: s.toCharArray()) {
            if (isOpenParenthsis(parenthesis)) {
                stack.add(parenthesis);
            } else {
                if (stack.size() < 1) {
                    return false;
                }

                char poped = stack.get(stack.size() - 1);
                if (poped == '(' && parenthesis == ')'
                    || poped == '{' && parenthesis == '}'
                    || poped == '[' && parenthesis == ']') {
                        stack.remove(stack.size() - 1);
                } else {
                    return false;
                }
            }
        }

        return stack.size() == 0;
    }

    private boolean isOpenParenthsis(char p) {
        return p == '(' || p == '{' || p == '[';
    }
}
```