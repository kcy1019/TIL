## 956. Tallest Billboard

[Problem Link](https://leetcode.com/contest/weekly-contest-114/problems/tallest-billboard/)

#### 문제

n개의 수들 중 일부를 뽑아 합이 같은 두 묶음으로 나눌 때, 만들 수 있는 최대의 합을 구하자.

#### 제한

* `0 ≤ n ≤ 20`
* `sum(a) ≤ 5000`
* `1 ≤ a[i] ≤ 1000`

#### 풀이

유명한 부류의 문제! knapsack과 결국엔 동치인데, 전체의 합이 5000으로 작으니까 [pseudo polynomial 시간 복잡도를 갖는 동적계획법(dp)](https://en.wikipedia.org/wiki/Knapsack_problem#Dynamic_programming_in-advance_algorithm)으로 접근하는게 좋다.
사실 처음에는 `n ≤ 20` 만 보고 생각 없이 bitmask dp를 짜서 MLE, TLE를 겪다가 뒤늦게 그럴 필요가 없다는 것을 깨달았는데.. 그 과정도 정리해 봤다.


##### TLE

`f(mask, diff) = mask개의 수를 양 묶음에 적절히 배치해서 차이를 diff로 만들 수 있는지의 여부` 로 정의하고, 각 `mask` 에 대해 `diff = 0` 을 만들 수 있는지 체크한다.
만들 수 있는 경우 `sum(a[mask]) / 2` 가 각 묶음의 합이 된다.
사실 이렇게 하면 최악의 경우 메모리 사용량이 `2^20 * 5000` 에 가깝게 되어 TLE 이전에 MLE가 나게 되므로, `map` 을 이용하고,
또 `map` 의 엔트리 개수가 되도록이면 적게 생성되도록 소소한 최적화를 했다.
결국 아래의 코드는 합이 큰 순서대로 수 조합(bitmask)을 정렬해서 시도해보는 방식을 이용해서 탐색량을 줄였으나,
테스트 65/74에서 TLE의 벽에 가로막혔다.

```cpp
int n;
vector<int> v;
vector<short> s;
vector<map<short, bool>> d;

short f(int left, short diff) {
    if (!left) return !diff;
    if (s[left] == abs(diff)) return true;
    if (s[left] < abs(diff)) return false;
    if (d[left].count(diff)) return d[left][diff];

    const int i = __builtin_ffs(left) - 1;
    return d[left][diff] = f(left ^ (1 << i), diff - v[i]) ||
                           f(left ^ (1 << i), diff + v[i]);
}

struct Solution {
    int tallestBillboard(vector<int>& r) {
        v = r;
        n = v.size();
        d.clear(), s.clear();
        d.resize(1 << n), s.resize(1 << n);
        vector<int> u;
        for (int i = 0; i < n; i++) s[1 << i] = v[i];
        for (int i = 1; i < 1 << n; i++) {
            s[i] = s[i ^ (i&-i)] + s[i&-i];
            if (s[i] & 1) continue;
            u.emplace_back(i);
        }
        int ans = 0;
        sort(u.begin(), u.end(), [&] (const int x, const int y) { return s[x] > s[y]; });
        for (const auto& p: u) {
            if (s[p] <= ans) break;
            if (f(p, 0)) ans = s[p];
        }
        return ans >> 1;
    }
};
```

##### 다시 knapsack으로

다시 생각해보니 `n ≤ 20` 이라는 말에 너무 쉽게 넘어가서 비트마스크를 쓴 것 같아서 -\_-
처음으로 돌아가니 문제를 knapsack의 dp를 그대로 이용해서 해결할 수 있음을 깨달았다.
`d(i, diff) = i번 수 까지 고려했을 때(즉 a[0:i+1] 만 이용했을 때), 두 묶음의 합 차이가 diff인 경우에 만들 수 있는 가장 큰 합`
이렇게 정의하면 정말 클래식한 점화식이 나온다. (실제로는 아마 아래 코드보다도 더 줄일 수 있을 것으로 보인다)
`diff = (묶음 1의 합) - (묶음 2의 합)` 으로 정의하면 a[i]를 첫 번째 묶음에 추가할 경우 `diff = diff + a[i]`,
두 번째 묶음에 추가할 경우 `diff = diff - a[i]` 가 되고, `diff` 의 부호가 바뀌는 경우에만 주의하면 직관적이고 깔끔한 식이 나온다.

```cpp
struct Solution {
    int tallestBillboard(vector<int>& v) {
        int n = v.size();
        map<int, int> d[21];
        d[0][0] = 0;
        for (int i = 1; i <= n; i++) {
            for (auto& j: d[i-1]) {
                d[i][j.first] = max(d[i][j.first], j.second);
                if (j.first < 0) {
                    int ov = max(0, j.first + v[i-1]);
                    d[i][j.first + v[i-1]] = max(d[i][j.first + v[i-1]], j.second + ov);
                    d[i][j.first - v[i-1]] = max(d[i][j.first - v[i-1]], j.second + v[i-1]);
                } else {
                    int ov = -min(0, j.first - v[i-1]);
                    d[i][j.first - v[i-1]] = max(d[i][j.first - v[i-1]], j.second + ov);
                    d[i][j.first + v[i-1]] = max(d[i][j.first + v[i-1]], j.second + v[i-1]);
                }
            }
        }
        return d[n][0];
    }
};
```
