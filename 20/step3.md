# Step3 3回続けて10分以内に書いてエラーを出さなければOKとする
2:20 ~ 2:50でAcceptできた。

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