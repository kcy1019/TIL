# 673. Number of Longest Increasing Subsequence

[Problem Link](https://leetcode.com/problems/number-of-longest-increasing-subsequence/)

## 문제

주어진 수열에서 일부 항을 없애 얻을 수 있는 [부분수열][subseq] 중에서 수열의 각 항이 순증가하는 형태(`i < j 이면 a[i] < a[j]`)를 가지는 것을 찾았을 때, 가장 긴 수열, 즉 [최장 증가 부분수열(이하 LIS)][longest-subseq] 의 개수는 몇 개일까?

### 제한

* `0 ≤ n ≤ 2000`
* `0 ≤ answer ≤ INT_MAX`

## 풀이

아주 유명한 동적 계획법 문제! O(n^2) 방법과 O(nlogn) 방법을 모두 살펴보자.

### O(n^2) 방법

`d[i] = a[0:i+1]로 만들 수 있는 LIS 중 a[i]로 끝나는 것의 길이`라고 정의하면, 점화식은 `d[i] = max(filter(x -> x < a[i], d[0:i])) + 1`라고 간단하게 나타낼 수 있다.

```cpp
int lis_len_only_slow(const vector<int> & seq)
{
    vector<int> d(seq.size(), 1);

    for (int i = 1; i < seq.size(); i++) {
        for (int j = 0; j < i; j++)
            if (seq[j] < seq[i] && d[j] + 1 > d[i])
                d[i] = d[j] + 1;
    }

    return *max_element(d.begin(), d.end());
}
```

길이는 이렇게 쉽게 구할 수 있다. 개수를 세기 위해서는 어떻게 해야 할까?<br>
사실 길이와 개수를 동시에 세면 쉽게 생각해낼 수 있다.<br>
`c[i] = a[0:i+1]로 만들 수 있는 LIS 중 a[i]로 끝나는 것의 개수`라고 정의하자.<br>
즉 이 정의 하에서는 `a[j] < a[i]` 이면서 `d[j]+1 == d[i]`인 `j`들의 `sum(c[j])`가 바로 `c[i]`가 된다.<br>
이를 계산하기 위해서는 우선 `d[i] = d[j] + 1`로 업데이트 될 때, `c[i] = c[j]` 를 수행해줘야 하고,<br>
한 가지 경우를 더 생각해보면 된다: `d[i] == d[j] + 1`인 경우
(예를 들어 `a[0:i+1] = {1, 2, 3, 3, 5}` 인 경우 `a[i] (= 5)`를 끝으로 갖는 길이가 4인 수열은 2개(`d[2] + 1, d[3] + 1`) 존재),<br>`c[i] += c[j]`를 통해 해당하는 모든 경우를 더해줘야 한다.

```cpp
int lis_cnt_slow(const vector<int> & _seq)
{
    vector<int> d{0}, c{1}, seq = _seq;
    seq.insert(seq.begin(), numeric_limits<int>::min());

    for (int i = 1; i < seq.size(); i++) {
        d.emplace_back(0);
        c.emplace_back(0);
        for (int j = 0; j < i; j++) if (seq[j] < seq[i]) {
            if (d[j] + 1 > d[i]) {
                d[i] = d[j] + 1;
                c[i] = c[j];
            } else if (d[j] + 1 == d[i]) {
                c[i] += c[j];
            }
        }
    }

    int ans = 0, ans_len = *max_element(d.begin(), d.end());
    for (int i = 1; i < seq.size(); i++) if (d[i] == ans_len) {
        ans += c[i];
    }
    return ans;
}

struct Solution {
    int findNumberOfLIS(vector<int>& nums) {
        return nums.empty() ? 0 : lis_cnt_slow(nums);
    }
};
```

### O(nlogn) 방법

이 문제는 `0 ≤ n ≤ 2000` 이므로 O(n^2)으로도 충분히 풀리지만, 제한이 클 경우에 대해 생각해보자.<br>
이런 경우에도 이분탐색을 이용하면 시간 복잡도를 O(nlogn)까지 줄여서 해결할 수 있다.<br>
`d[i] = 길이가 i+1인 LIS의 마지막 수 중 최솟값`라고 다시 정의해보자.<br>
이렇게 정의할 경우 [`std::lower_bound`][lower-bound]를 이용하여
입력 수열의 각 원소에 대해 O(logn)의 시간 복잡도로 `d[]`를 갱신할 수 있다.

```cpp
int lis_len_only(const vector<int> & seq)
{
    vector<int> d;

    for (auto& p: seq) {
        auto nxt = lower_bound(d.begin(), d.end(), p);
        if (nxt == d.end()) d.emplace_back(p);
        else *nxt = p;
    }

    return d.size();
}
```

길이를 구하는 코드는 이번에도 간단하다. 개수를 세기 위해서는 어떻게 바꾸면 좋을까?<br>
앞의(O(n^2)) 때의 조건을 다시 살펴보자: `a[j] < a[i]` 이면서 `d[j]+1 == d[i]`인 `j`들의 `sum(c[j])`가 바로 `c[i]`가 된다.<br>
이를 이분탐색을 이용해서 구하기 위해서는 이전(`lis_len_only`)의 `d[]`를 확장하기 위해 조금 복잡한 자료구조를 떠올려야 한다.<br>
`d[i] = [pair(길이가 i+1인 LIS의 마지막 수, (해당하는 수열의 개수) + (길이가 i+1 이면서 마지막 수가 더 큰 LIS의 개수)), ...] (최소 수가 아니라 *모든* 마지막 수를 저장하는 배열이다)`
로 확장해서 정의하고, 이전에 사용하던 `d[]`는 `dmin[]`으로 이름을 바꾸자.<br>
이 정의 아래에서는 앞선 조건에 해당하는 `c[j]`의 합을 또 [`std::lower_bound`][lower-bound]를 통해 빠르게 구할 수 있다.<br>
마지막 수에 대해 이분탐색을 수행해서 `a[j] ≥ a[i]`인 `j`를 찾고, 해당하는 수열의 개수를 얻으면 되는데,
이 개수는 `d[i-1][0]`의 수열 개수에서 `d[i-1][j]`의 수열 개수를 빼 줌으로써 구할 수 있다([partial sum][partial-sum]의 아이디어).

```cpp
int lis_cnt(const vector<int> & seq)
{
    vector<int> dmin;
    vector<deque<pair<int,int>>> d;

    for (auto& p: seq) {
        auto nxt = lower_bound(dmin.begin(), dmin.end(), p);
        const int i = nxt - dmin.begin();
        int cnt = 1;
        if (i > 0) {
            auto bound = lower_bound(d[i-1].begin(), d[i-1].end(), make_pair(p, 0));
            cnt = d[i-1][0].second - (bound == d[i-1].end() ? 0 : bound->second);
        }
        if (nxt == dmin.end()) {
            d.emplace_back();
            d[i].emplace_back(p, cnt);
            dmin.emplace_back(p);
        } else {
            d[i].emplace_front(p, d[i][0].second + cnt);
            dmin[i] = p;
        }
    }

    return d.back()[0].second;
}

struct Solution {
    int findNumberOfLIS(vector<int>& nums) {
        return nums.empty() ? 0 : lis_cnt(nums);
    }
};
```

실제로 답안을 제출해보면 두 방법의 수행 시간이 각각 36ms, 12ms로 작은 입력에 대해서도 차이를 보이는 것을 확인할 수 있다.

[longest-subseq]: https://ko.wikipedia.org/wiki/%EC%B5%9C%EC%9E%A5_%EC%A6%9D%EA%B0%80_%EB%B6%80%EB%B6%84_%EC%88%98%EC%97%B4
[subseq]: https://ko.wikipedia.org/wiki/%EB%B6%80%EB%B6%84%EC%88%98%EC%97%B4
[lower-bound]: https://ko.cppreference.com/w/cpp/algorithm/lower_bound
[partial-sum]: https://en.wikipedia.org/wiki/Prefix_sum
