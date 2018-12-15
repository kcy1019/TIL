# 955. Delete Columns to Make Sorted II

[Problem Link](https://leetcode.com/contest/weekly-contest-114/problems/delete-columns-to-make-sorted-ii/)

#### 문제

n x m 크기의 문자열 행렬이 주어진다.
이 행렬에서 몇 개의 열을 지워서 각 행이 나타내는 문자열이
위에서부터 차례로 정렬된 순서를 가지도록 하려면
적어도 몇 개의 열을 지워야 할까?

#### 제한

* `0 ≤ n ≤ 100`
* `1 ≤ a[i].length() ≤ 100`
* `islower(a[i][j]) && isalpha(a[i][j]) == true`

#### 풀이

관점을 바꿔서, 열을 **지우는 대신** 반대로 한 개씩 골라 **붙여** 정렬된 스트링을 최대한 길게 만드는 문제를 생각해보자. 이렇게 할 경우, 전체 열 개수에서 선택한 열의 개수를 빼면 원래 문제의 정답인 '지워야 할 열의 개수'를 얻을 수 있다.

이제 각 열을 붙이기 위한 조건을 생각해보자.

* 첫 번째로 선택하는 열은 정렬되어 있어야 한다(`a[i][col] ≤ a[i+1][col]`).
* 그 이후로 선택하는 열은, 일부 행이 정렬되어 있어야 한다.
    * 이전에 선택한 열에서 '같은 값을 가지는 행'이 연속된 부분에 대해서만 확인하면 된다.
      즉, 이전에 선택한 열이 `"abbbcdef"`인 경우, `"zaaczfba"`를 확인할 때에는 `"aac"`의 정렬 여부만 확인하면 된다.

이 과정을 계속 반복해나가면 답이 나오는 것은 직관적으로 납득할 수 있다.
납득할 수 없는 경우 조건을 뒤집어서 생각해보자: 정렬되지 않은 열을 선택할 경우 답이 될 수 없는건 자명하고, 정렬 상태를 깨지 않는 열을 새로 선택하지 않는 것은 최적이 아님 또한 자명하다.


```cpp
#include<cstdio>
#include<string>
#include<queue>
#define sz size()
using namespace std;

template<class T, class V>
bool is_sorted(T & a, V & ignore, int col) {
    for (int row = 0; row + 1 < a.sz; ++row) {
        if (!ignore[row] && a[row][col] > a[row+1][col])
            return false;
    }
    return true;
}

template<class T, class V>
void insert_column(T & a, V & ignore, int col) {
    for (int row = 0; row + 1 < a.sz; ++row) {
        if (ignore[row]) continue;
        if (a[row][col] < a[row + 1][col])
            ignore[row] = true;
    }
}

struct Solution {
    int minDeletionSize(vector<string>& a) {
        int ans = 0;
        vector<bool> already_sorted(a.sz);
        for (int col = 0; col < a[0].sz; ++col) {
            if (is_sorted(a, already_sorted, col)) {
                insert_column(a, already_sorted, col);
                ++ans;
            }
        }
        return a[0].sz - ans;
    }
};
```
