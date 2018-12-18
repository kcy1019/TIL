# 960. Delete Columns to Make Sorted III

[Problem Link](https://leetcode.com/contest/weekly-contest-115/problems/delete-columns-to-make-sorted-iii/)

#### 문제

n x m 크기의 문자열 행렬이 주어진다.
이 행렬에서 몇 개의 열을 지웠을 때 각 행의 문자열이
정렬된 상태를 가지도록 하려면
적어도 몇 개의 열을 지워야 할까?

#### 제한

* `0 ≤ n ≤ 100`
* `1 ≤ a[i].length() ≤ 100`
* `islower(a[i][j]) && isalpha(a[i][j]) == true`

#### 풀이

[이전 문제와 마찬가지로][sorted-ii] 열을 지우는 대신 반대로 한 개씩 골라 붙여 정렬된 스트링을 최대한 길게 만드는 문제를 생각해보자. 이렇게 하면 동적계획법을 이용해서 생각보다 간단하게 해결할 수 있다.

`d[i]: i번째 열로 끝나는 가장 긴 문자열의 길이` 라고 정의하자.
이렇게 해도 되는 이유는, 다음에 올 수 있는 열의 조건이 오직 마지막으로 선택한 열에 의해 결정되기 때문이다.
점화식 역시 간단하게 해결할 수 있다. 제한이 작기 때문에 [이전의 O(n^2) LIS][lis] 때와 거의 똑같은 식을 이용하면 된다.

```cpp
#include<string>
#include<queue>
using namespace std;

inline bool right_order(const vector<string>& A, int c1, int c2) {
    for (auto& row: A) {
        if (row[c1] > row[c2])
            return false;
    }
    return true;
}

struct Solution {
    int minDeletionSize(const vector<string>& A) {
        vector<int> d(A[0].size());
        for (int i = 0; i < A[0].size(); i++) {
            d[i] = 1;
            for (int j = 0; j < i; j++)
                if (right_order(A, j, i))
                    d[i] = max(d[i], d[j] + 1);
        }
        return A[0].size() - *max_element(d.begin(), d.end());
    }
};
```

[sorted-ii]: leetcode-955-delete-columns-to-make-sorted-ii.md
[lis]: leetcode-673-number-of-longest-increasing-subsequence.md
