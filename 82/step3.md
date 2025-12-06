# Step3 3回続けて10分以内に書いてエラーを出さなければOKとする

## 1回目(エラー), 2回目(AC)
- nodeCountMapにわたす関数名をミス。`num`ではなく、`sum`
- foreachで回す変数は`nodeCountMap`ではなく、`nodeCountMap.entrySet()`
- nodeValue, nodeCountは`entrySet`からgetする
- 上記の文法エラーを解消してAC。6:23。

## 3回目(エラー), 4回目(AC)
- whileの条件エラー。`while(node != null)`を`while(node.next != null)`に誤り。直してAC。
```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) {
            return null;
        }

        ListNode node = head;
        Map<Integer, Integer> nodeCountMap = new TreeMap<Integer, Integer>();
        while (node.next != null) { // <- ミス
            nodeCountMap.merge(node.val, 1, Integer::sum);
            node = node.next;
        }

        // 初期値にはダミーのノードを設定する。
        ListNode newHead = new ListNode();
        ListNode tail = newHead;
        for (Map.Entry<Integer, Integer> entry: nodeCountMap.entrySet()) {
            int nodeValue = entry.getKey();
            int nodeCount = entry.getValue();
            if (nodeCount != 1) {
                continue;
            }

            tail.next = new ListNode(nodeValue);
            tail = tail.next;
        }

        return newHead.next;
    }
}
```

## 5,6,7回目(AC)
- 平均 2分25秒くらいでAC。
- `head == null`はなくても動く。
```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) {
            return null;
        }

        ListNode node = head;
        Map<Integer, Integer> nodeCountMap = new TreeMap<>();
        while (node != null) {
            nodeCountMap.merge(node.val, 1, Integer::sum);
            node = node.next;
        }

        // 初期値にダミーのノードを設定する。
        ListNode newHead = new ListNode();
        ListNode tail = newHead;
        for (Map.Entry<Integer, Integer> entry: nodeCountMap.entrySet()) {
            int nodeValue = entry.getKey();
            int nodeCount = entry.getValue();
            if (nodeCount != 1) {
                continue;
            }

            tail.next = new ListNode(nodeValue);
            tail = tail.next;
        }

        return newHead.next;
    }
}
```