# Step2 他の人のコードを読んで、コードを整える

## コードを整える
- 片方のリストが終端まで到達した場合、値が0のダミーのノードを使って計算することで、1つのループにまとめる
- l1,l2のnullチェックはなくても動くが、無駄な走査が走る。
- ノードがnullのときにダミーのノードを作成するため、結果のためのノードの数 + (長い方のリスト長 - 短い方のリスト長)個のノードが作成される。空間計算量としてはO(n)でもとと変わらない。
- 実用的には引数のノードを破壊しないほうがよいので、list1, list2に詰め直している。
```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        if (l1 == null) {
            return l2;
        }
        if (l2 == null) {
            return l1;
        }

        ListNode dummy = new ListNode();
        ListNode result = dummy;
        int adder = 0;

        ListNode list1 = l1;
        ListNode list2 = l2;
        while (list1 != null || list2 != null) {
            if (list1 == null) {
                list1 = new ListNode(0);
            }
            if (list2 == null) {
                list2 = new ListNode(0);
            }
            int sum = list1.val + list2.val + adder;
            int nodeValue = sum % 10;
            adder = sum / 10;

            result.next = new ListNode(nodeValue);
            result = result.next;
            list1 = list1.next;
            list2 = list2.next;
        }

        if (adder == 1) {
            result.next = new ListNode(1);
        }
        return dummy.next;
    }
}
```

## 他の人のコードを読む
- https://github.com/hiroki-horiguchi-dev/leetcode/pull/5/files
  - 繰り上がりの情報は`carry`の方が適切だった。`adder`は加算機だった。
  - 結果のノードを作る時に`ListNode`のコンストラクタに直接`n % 10`を渡していた。確かに中間変数はなくてもよかったかも。
  - dummyHeadのコンストラクタに値は渡さなくてよいかなと思った。dummyという変数名でただの置物なのはわかるが、あえて`-1`を渡す理由もないので。
    - ただ、`ListNode()`というコンストラクタが用意されているとはいえ、`val`の初期値を何も指定せずにインスタンス化するのは若干気持ち悪い。int型の変数を初期値を指定せずに宣言すると値は`0`になるが。。。
      - これって言語仕様としてどこに書かれている？
      - [The Java Language Specification - 4.12.5 Initial Values of Variables](https://docs.oracle.com/javase/specs/jls/se25/html/jls-4.html#jls-4.12.5)
        - > For type `int`, the default value is zero, that is, 0.
      - ちなみに `boolean` の初期値は `false` だそう。まあ値未設定なら `false` であってほしいかも。
  - `l1Head`,`l2Head`はループの中で更新されてheadではなくなるので、`Head`は紛らわしいかも。
  - `tempSum` -> たしかにノードの場合に応じて合計値を順に足しているので`temp`をつけたくなる気持ちも分かるが、最終的な合計値を入れる値だから`sum`でもいいかなと思う。
    - 同様の指摘あり。
  - `tail`は慣習的に使うからこれでもよいのかな？ただ今回のケースで`tail`はリストの末尾のノードを指していますよ、と伝えるだけだとあまり旨味はないように感じる。
    - もともと`dummyhead`と`tail`はそれぞれ`result`と`pointer`だった。そこで、それぞれの変数の役割と変数名に距離があるという指摘があって修正されていた。
    - 変数名は、手続きの中においてどのような役割であるかを表す名前が良さそう。なので自分も`result`から変えておく。
      - 自分は結果を格納するから`result`だろ、と反射神経的に命名していたので、改めて命名については熟考してつけます。
    - 「`result`を返すんじゃないだとびっくりしました。」 => たしかに`result`なら変数をそのまま返却されることを期待するのが読み手の期待かもしれない。
  - `l1Head`, `l2Head`のnullチェックが別れて記述されているので、まとめたい。
    - 同様の指摘有り。ただ、自分はどうまとめるか考えてなかった。
  - 「`carry`で初期化すれば43行目の加算が不要になる」 => なるほど！
  - `if (carry == 1)`のブロックはwhileのOR条件として追加すればけずれる。 => たしかに。ただ、末端処理として別に書かれてても自分はいいかなと思う。コメントで「最上位の桁の計算で繰り上がりがある場合値が1のノードを追加する」と書いておくとより親切かもと思った。
- コメント集
  - `carry` は `boolean` か `int` か
    - これは自分も同じこと思ったが、加算処理につかいやすくするために `int` を選択した。
    - 「整数が入るが結果として0,1しか入らないと理解」
    - 「3つ以上のLinkedListの足し算をする関数を用意したら繰り上がりは2以上になる」=>拡張性の面でも`int`で良さそう。
  - zip_longest -> たしかにPython触ってた時の記憶でこの問題はzip操作だなとは思ったが、Javaには無さそうなので候補からはずしていた。

- コードの整形
  - 関数頭のnullチェックを削除
  - `result` は `tail`として、計算結果のリストの末尾のノードを指すこと示す名前に変更
  - `adder` ではなく `carry`
  - `carry == 1` を while のOR条件に追加して、末尾の`carry == 1`のifを削除
  - `nodeValue` を削除して `ListNode`のコンストラクタに直接渡す。
```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode();
        ListNode tail = dummy;
        int carry = 0;

        ListNode list1 = l1;
        ListNode list2 = l2;
        while (list1 != null || list2 != null || carry == 1) {
            if (list1 == null) {
                list1 = new ListNode(0);
            }
            if (list2 == null) {
                list2 = new ListNode(0);
            }
            int sum = list1.val + list2.val + carry;
            carry = sum / 10;

            tail.next = new ListNode(sum % 10);
            tail = tail.next;
            list1 = list1.next;
            list2 = list2.next;
        }

        return dummy.next;
    }
}
```
