# Step1 何も見ずに解く
2. Add Two Numbers

- 2つのLinkedListが用意されて、書くノードが桁に対応している。2つのLinkedListを足し合わせた合計値をLinkedListとして表現する。
- 解法検討
  - 解法1: 
    - 結果のリストを用意しておく
    - 2つのLinkedListを頭からたどる。
    - それぞれのノードの値と繰り上がりの値を足し合わせるて、結果をノードの値として、結果のリストに連結する。。
      - 合計値が10以上の場合、10で割ったあまりをノードの値として、結果のリストに連結。また、繰り上がりを記録しておく。
    - どちらかのノードがnullになるまで繰り返す。
    - nullがでたらnullがないほうのノードを辿って、ノードの値をそのまま採用して結果のリストに連結。
    - 計算量
      - 時間計算量: O(n) リストの長さは最大100で2つたどってもO(200)
      - 空間計算量: O(n) 新しいリストを作る最大長は101。(100桁+100桁ですべて9なら繰り上がって101桁)
  - 解法2:
    - 長い方のリストを特定して、短いリストと長いリストを順にたどる。短いリストのノードの値を長いリストの方のノードに足す。10未満なら、結果の値をノードの値として上書き、10以上なら10で割ったあまりをノードの値として上書き、繰り上がりを記録しておく。
    - 計算量
      - 時間計算量: O(n) 最初に両方の長さを知るためにそれぞれのリストを最後まで辿る必要がある。
      - 空間計算量: O(1) 片方のリストに上書きする
  - もとのリストを上書きするか、新しいリストを作るかの違いでしかない。今回の入力値の範囲では空間計算量が問題にならないはずなので、解法(1)で実施。
- 検討: 15分ほど

- 初回提出: Time Limit Exceeed
- その後何度かエラー
  - l1, l2の更新忘れ
  - resultの更新忘れ
  - l1, l2が同じ長さ かつ 最後に繰り上がりがあるケースの考慮漏れ
- 上記を直しつつ、AC。
- 愚直に上から書き下しているので汚い。
- 疑問
  - `n % 10`で一の桁の値を、intの変数を`n / 10`して十の位の値を取得するのは、常識として扱ってよいか？
- 解答 15分ほど
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
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
        while (l1 != null && l2 != null) {
            int sum = l1.val + l2.val + adder;
            int nodeValue = sum % 10;
            adder = sum / 10;

            result.next = new ListNode(nodeValue);
            result = result.next;
            l1 = l1.next;
            l2 = l2.next;
        }

        ListNode remainNode = null;
        if (l1 == null && l2 == null) {
            if (adder == 1) {
                result.next = new ListNode(1);
            }
            return dummy.next;
        } else if (l1 == null) {
            remainNode = l2;
        } else {
            remainNode = l1;
        }

        while (remainNode != null) {
            int sum = remainNode.val + adder;
            int nodeValue = sum % 10;
            adder = sum / 10;

            result.next = new ListNode(nodeValue);
            result = result.next;
            remainNode = remainNode.next;
        }

        if (adder == 1) {
            result.next = new ListNode(1);
        }
        return dummy.next;
    }
}
```