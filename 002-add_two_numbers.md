# 002-Add Two Numbers

## Question

> You are given two linked lists representing two non-negative numbers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.
>
> ```
> Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
> Output: 7 -> 0 -> 8
> ```


这是大数加法的简化版本，大数加法一般用模拟竖式的方法做，从低位向高位做，所以数字是反向存储的，加的时候注意进位就好。

## Solution

- 循环

	同时遍历两个链表（即从低向高相加的过程），相应的位相加再加上进位的数，得到结果的每一位

- 还有更牛逼的解法么 ==

## Code

### Extra

单链表定义如下：

```python
# python
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None
```

```cpp
// cpp
struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(NULL) {}
};
```


### python

```python
class Solution:
    # @return a ListNode
    def addTwoNumbers(self, l1, l2):
        head = ListNode(0)
        current = head
        remain = 0
        while l1 is not None or l2 is not None:
            if l1:
                remain += l1.val
                l1 = l1.next
            if l2:
                remain += l2.val
                l2 = l2.next

            current.next = ListNode(remain % 10)
            remain /= 10
            current = current.next

        # 不要忘了可能剩下的进位哦
        if remain != 0:
            current.next = ListNode(remain)

        return head.next
```

### cpp

```cpp
class Solution {
public:
    ListNode *addTwoNumbers(ListNode *l1, ListNode *l2) {
        ListNode *head = new ListNode(0),
                 *current = head;
        int remain = 0;
        while (l1 != nullptr || l2 != nullptr) {
            if (l1 != nullptr) {
                remain += l1->val;
                l1 = l1->next;
            }
            if (l2 != nullptr) {
                remain += l2->val;
                l2 = l2->next;
            }

            current->next = new ListNode(remain % 10);
            remain /= 10;
            current = current->next;
        }

        // 不要忘了可能剩下的进位哦
        if (remain != 0) {
            current->next = new ListNode(remain);
        }

        return head->next;
    }
};
```

### cpp code challenge

咳咳，我是发言者zyx，上述cpp代码可以有几点优化：

- remain不可能大于等于20，因而，可以用减来代替%和/
- l1或l2有一个为NULL时，可直接将剩下那个链到结果链表的后面
- head没有delete，内存泄露。

综上，新cpp代码：

```cpp
class Solution {
public:
    ListNode *addTwoNumbers(ListNode *l1, ListNode *l2) {
        ListNode *head = new ListNode(0),
                 *current = head;
        int remain = 0;
        while (l1 != nullptr || l2 != nullptr || remain != 0) {
            if (l1 == nullptr && remain == 0) {
                current->next = l2;
                break;
            }
            if (l2 == nullptr && remain == 0) {
                current->next = l1;
                break;
            }

            if (l1 != nullptr) {
                remain += l1->val;
                l1 = l1->next;
            }
            if (l2 != nullptr) {
                remain += l2->val;
                l2 = l2->next;
            }
            if (remain >= 10) {
                current->next = new ListNode(remain - 10);
                remain = 1;
            } else {
                current->next = new ListNode(remain);
                remain = 0;
            }
            current = current->next;
        }

        current = head->next;
        // do not forget to delete the useless node
        delete head;
        return current;
    }
};
```

### reply to the challenge

针对大神祥子的 challenge 我有点不同意见，hiahiahiahia

- remain不可能大于等于20，因而，可以用减来代替%和/

	这个木有意见，我测试了 `%` 和 `-`，大概有 50% 的提升，请看 `testArea` 里面的
	`test_remainders_divisions.cpp`

- l1或l2有一个为NULL时，可直接将剩下那个链到结果链表的后面

	这点我觉得，返回的链表应该是个新的才对，如果传入的链表做了一些其他操作，
	不应该影响到结果这个链表。

	另外代码更加简洁哦~

- head没有delete，内存泄露。

	I have failed this city, 不过祥子你是不是掉了 `head->next = nullptr;`
	
### final cpp code

咳咳，我是发言者zyx，与God Mao达成共识，根据上一节的第二点和第三点进行修改，得到最终版代码：

```cpp
class Solution {
public:
    ListNode *addTwoNumbers(ListNode *l1, ListNode *l2) {
        ListNode *head = new ListNode(0),
                 *current = head;
        int remain = 0;
        while (l1 != nullptr || l2 != nullptr || remain != 0) {
            if (l1 != nullptr) {
                remain += l1->val;
                l1 = l1->next;
            }
            if (l2 != nullptr) {
                remain += l2->val;
                l2 = l2->next;
            }
            if (remain >= 10) {
                current->next = new ListNode(remain - 10);
                remain = 1;
            } else {
                current->next = new ListNode(remain);
                remain = 0;
            }
            current = current->next;
        }
        
        current = head->next;
        head->next = NULL;
        delete head;
        return current;
    }
};
```
