66. 加一

思路：
从末尾开始遍历，若为9，则赋值0，第一个不为9的数加1。如果都为9，创建长度+1的新数组，并把首位赋值1。
```cpp
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        for (int i = digits.size() - 1; i >= 0; i--) {
            if (digits[i] == 9) {
                digits[i] = 0;//末尾为9的所有数变为0
            }
            else {
                digits[i]++;//第一个不为9的数加1
                return digits;
            }
        }
        vector<int> ans(digits.size()+1);//若都为9，则创建长度+1的新数组
        ans[0] = 1;//首位赋值1
        return ans;
    }
};
```
复杂度分析
时间复杂度：O(n)
空间复杂度：O(1)



21. 合并两个有序链表

思路：
创建新链表head，判断list1和list2当前的值，谁小，就让head指向谁，并更新list1或list2，当list1或list2为空时，让head指向不为空的那个链表。
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        ListNode* protect = new ListNode();//保护节点
        ListNode* head = protect;//新链表head
        while (list1 && list2) {
            //若list1的值小于等于list2的值，将head指向list1
            if (list1 -> val <= list2 -> val) {
                head -> next = list1;
                //更新head和list1
                head = head -> next;
                list1 = list1 -> next;
            }
            //否则指向list2
            else {
                head -> next = list2;
                //更新head和list2
                head = head -> next;
                list2 = list2 -> next;
            }
        }
        //判断list1是否为空，若不为空则直接让head指向list1，反之指向list2
        head -> next = list1 != nullptr ? list1 : list2;
        return protect -> next;
    }
};
```
复杂度分析
时间复杂度：O(n + m)，其中 n 和 m 分别为两个链表的长度。
空间复杂度：O(1)
