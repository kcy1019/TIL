# LeetCode Weekly Contest 116

[Contest Link](https://leetcode.com/contest/weekly-contest-116) [Leaderboard](https://leetcode.com/contest/weekly-contest-116/ranking/2/)

PS 대회는 너무 오랜만이라 좀 어려웠다ㅠㅠ
기하 문제는 생각을 잘못 하고, 자료형 틀리고 아주 난리..
그럼에도 불구하고 LeetCode는 문제 난이도가 비교적 도전해볼만 했다.

## 1. N-Repeated Element in Size 2N Array

**문제**

길이가 2n (4 ≤ n ≤ 10,000) 인 배열이 주어진다.
배열 내에서 n개의 수는 딱 1번씩만 등장하고, 1개의 수가 n번 등장한다.
이 n번 등장하는 수는 무엇일까?

**풀이**

정렬이나 카운팅을 통해 O(nlogn)으로 해결하는게 가장 무난하다.

```cpp
struct Solution {
    int repeatedNTimes(vector<int>& a) {
        sort(a.begin(), a.end());
        for (int i = 0; i+1 < a.size(); i++) {
            if (a[i] == a[i+1]) return a[i];
        }
    }
};
```

## 2. Maximum Width Ramp

**문제**

길이가 n (2 ≤ n ≤ 50,000) 인 배열이 주어진다.
`j < i && A[j] ≤ A[i]`인 `(j, i)` 중, `i - j`의 최댓값은 얼마일까?

**풀이**

역시 정렬을 이용하면 되는데, 비교는 값으로 하되 인덱스를 정렬해야 한다.

```cpp
struct Solution {
    int maxWidthRamp(vector<int>& a) {
        vector<int> idx(a.size());
        iota(idx.begin(), idx.end(), 0);
        sort(idx.begin(), idx.end(), [&](const int x, const int y) {
                        return a[x] < a[y];
                    });
        int ans = 0, min_idx = a.size();
        for (auto& i: idx) {
            ans = max(ans, i - min_idx);
            min_idx = min(i, min_idx);
        }
        return ans;
    }
};
```

**TMI**

쓸데없이 무거운 풀이로는 Segment Tree 를 이용한 풀이도 있다. 이 역시 O(nlogn).

## 3. Minimum Area Rectangle II

**문제**

주어진 n (1 ≤ n ≤ 50) 개의 점 중에서 4개의 점을 골라 직사각형을 만들었을 때,
가장 큰 넓이는 얼마일까?

**풀이**

n이 작아서 간단하게 O(n^3logn)으로도 풀린다. O(n^2logn) 역시 가능하지만 대회에선 시간이 생명이니 O(n^3logn)을..
구현했는데, 엄청난 판단 미스 때문에 부끄럽게도 시간 내에 풀지 못했다ㅠㅠ

내 풀이는 점을 반시계방향으로 정렬한 다음(*), 사각형의 변이 순서대로 직각을 이루는지 확인하는 방식이었다.
다만 *에서 실수큰 실수를 했다. 각 사각형별로 한 점을 원점으로 잡고 정렬했어야 하는걸,
괜히 효율적으로 짠답시고 맨 처음에 전체 배열을 통으로, 그것도 원점 기준으로(...) 정렬해놓고 풀면서
왜 안되지?!?!?ㅠㅠ 하고 고민하는 바람에 시간만 잔뜩 날리고 풀지도 못했다.

사실 잘 생각해보면 점이 4개 뿐이라 정렬할 때 각도(반시계)는 굳이 고민할 필요도 없는데..

```cpp
#define x first
#define y second
inline double rect_area(vector<vector<int>>&& v) {
    double area = 0;
    vector<pair<int,int>> vec;
    for (int i = 0; i < 4; i++) {
        int j = (i + 1) % 4;
        vec.pb(v[j][0] - v[i][0], v[j][1] - v[i][1]);
        area += v[i][0] * v[j][1] - v[i][1] * v[j][0];
    }

    for (int i = 0; i < 3; i++) {
        int j = (i + 1) % 4;
        if (vec[i].x * vec[j].x != -(vec[i].y * vec[j].y))
            return 1e15;
    }

    return abs(area) / 2;
}

struct Solution {
    double minAreaFreeRect(vector<vector<int>>& p) {
        sort(p.begin(), p.end());
        double ans = 1e15;
        for (int a = 0; a < p.size(); a++) {
            for (int b = a+1; b < p.size(); b++)
                for (int c = b+1; c < p.size(); c++) {
                    vector<int> p4{p[b][0] + p[c][0] - p[a][0], p[b][1] + p[c][1] - p[a][1]};
                    if (binary_search(p.begin(), p.end(), p4))
                        ans = min(ans, rect_area(vector<vector<int>>{p[a], p[b], p4, p[c]}));
                }
        }
        return (ans > 1e13 ? 0 : ans);
    }
};
```

## 4. Least Operators to Express Number

**문제**

2 이상의 자연수 x를 가지고 target을 만들 때, 연산자 개수를 최소로 하면 몇 개가 필요할까?
사용 가능한 연산자는 `+, -, *, /` 인데, 이 때 나눗셈은 유리수 나눗셈이다.
사용 가능한 연산자에 괄호가 없음에 유의할 것!

**풀이**

우선 풀이에 앞서, 나눗셈은 1을 만들기 위한 용도밖에 쓸모가 없다.
그리고 뺄셈을 제외하고 생각해보면 문제가 엄청 단순한 unbounded knapsack, 그것도 그리디 접근으로 풀리는 문제로 바뀐다: `1, x, x * x, x * x * x, ...` 을 적절히 더해서 총 덧셈 연산자의 개수가 최소가 되도록 하여 `target`을 만드는 문제.
이 간략화된 버전의 문제는 무조건 큰 수부터 최대한 많이 이용하도록 풀면 간단하게 최적 해를 구할 수 있다.

```cpp
int no_subtract(int x, int target) {
    int x_pow = x, exponent = 1;
    while (x_pow * x <= target) x_pow *= x, ++exponent;

    int ans = 0;
    while (target > 0) {
        ans += target / x_pow * exponent;
        target %= x_pow;
        x_pow /= x;
        --exponent;
    }

    return ans - 1;
}
```

여기에 뺄셈이 들어가면 문제가 어떻게 바뀔까?
간단하게 아까의 `1, x, x * x, ...` 에다가 부호를 붙인 `-1, -x * x, ...` 이 추가된다.
이제 매 단계에서 선택 가능한 선택지(부호)가 생겨버렸기 때문에 아까처럼 마냥 그리디 접근으로 해결할 수는 없게 돼버렸지만,
여전히 동적계획법을 이용하면 비슷한 방법으로 어렵지 않게 해결할 수 있다.

```cpp
#define x first
#define y second
using ll = long long;

map<ll, ll> powers, d;
int xx = 0;

ll f(ll target)
{
    if (target == 0) return 0LL;
    if (powers.count(target)) return powers[target] + 1LL;
    if (d.count(target)) return d[target];
    ll& ret = d[target];
    ret = target * (powers[1] + 1LL);

    for (auto i = powers.rbegin(); i != powers.rend(); ++i) {
        if (((i->x) / xx > 0) && target % ((i->x) / xx) == target) continue; // 무의미하게 큰 지수는 무시
        ret = min(ret, f(target % i->x) + ll(target / i->x) * ll(i->y + 1LL));
        ret = min(ret, f((i->x - (target % i->x))) + f((target + i->x - 1) / i->x * i->x));
        ret = min(ret, f(target % i->x) + f(target - target % i->x));
    }

    return ret;
}


struct Solution {
    int leastOpsExpressTarget(int x, int target) {
        xx = x;
        powers.clear(), d.clear();

        ll exponent = 0, power = x;
        while (power < target) {
            power *= x;
            powers[power] = ++exponent;
        }
        powers[1] = 1;
        powers[x] = 0;

        return f(target) - 1;
    }
};
```
