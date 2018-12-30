# LeetCode Weekly Contest 117

[Contest Link](https://leetcode.com/contest/weekly-contest-117) [Leaderboard](https://leetcode.com/contest/weekly-contest-117/ranking/2/)

이번 대회는 (저번에 비해) 굉장히 쉬웠는데, 쉽다고 막 짰다가 실수를 엄청 많이 해 버렸다 ㅠㅠ

## Univalued Binary Tree

**문제**

이진트리가 주어졌을 때, 모든 노드에 매겨진 수가 같은지 판별해보자

**풀이**

Straightforward dfs.

```cpp
/*
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};
 */
struct Solution {
    bool isUnivalTree(TreeNode* root, int value = -1) {
        if (value == -1) value = root -> val;
        else if (root -> val != value) return false;
        if (root -> left && !isUnivalTree(root -> left, value))
            return false;
        if (root -> right && !isUnivalTree(root -> right, value))
            return false;
        return true;
    }
};

```

## Numbers With Same Consecutive Differences

**문제**

연속된 자리의 수의 차이가 딱 K만큼 나는 N자리의 음이 아닌 정수를 모두 찾아보자.
단, leading zero는 숫자 0 이외에 허용되지 않는다(즉 0은 가능하지만 00, 01은 안 된다).

예) `N = 3, K = 2 -> [131,135,202, ...]`

제한: `1 ≤ N ≤ 9, 0 ≤ K ≤ 9`

**풀이**

**모든 수** 를 찾아야 하므로 완전 탐색보다 더 효율적인 방법이 없단것만 기억하면 된다.

```cpp
#include<set>
using namespace std;
set<int> ret;

void dfs(int cur, int left, int diff)
{
    if (left == 0) {
        ret.insert(cur);
    } else if (cur == 0) {
        for (int i = 1; i < 10; i++)
            dfs(i, left - 1, diff);
    } else {
        int d = cur % 10;
        int x = d + diff;
        int y = d - diff;
        bool done[10]{};
        if (x >= 0 && x < 10 && !done[x])
            dfs(cur * 10 + x, left - 1, diff), done[x] = true;
        if (y >= 0 && y < 10 && !done[y])
            dfs(cur * 10 + y, left - 1, diff), done[y] = true;
    }
}

struct Solution {
    vector<int> numsSameConsecDiff(int N, int K) {
        ret.clear();
        if (N == 1) ret.insert(0);
        dfs(0, N, K);
        return vector<int>(ret.begin(), ret.end());
    }
};
```

## Vowel Spellchecker

**문제**

알파벳 대소문자로 이루어진 단어들로 구성된 사전(wordlist)과 쿼리가 주어졌을 때,
쿼리에 대한 사전 검색 결과를 다음과 같은 규칙에 맞춰 계산해보자.
규칙은 우선 순위가 높은 순서대로 다음과 같다:

1. 주어진 쿼리 문자열이 사전에 정확히 존재하는 경우 그대로 리턴
2. 주어진 쿼리 문자열에서 대소문자 여부만 바꾼게 사전에 존재하는 경우,
   첫 번째로 매치하는 사전의 문자열을 리턴
3. 주어진 쿼리 문자열에서 대소문자 여부와
   모음(`aeiou`)을 적절히 바꾼게 사전에 존재하는 경우,
   첫 번째로 매치하는 사전의 문자열을 리턴
4. 아무 규칙에도 맞지 않는 쿼리의 경우,
   빈 문자열을 리턴

제한

```ruby
1 ≤ |wordlist| ≤ 5000
1 ≤ |querylist| ≤ 5000
1 ≤ |querylit[i]| ≤ 7
1 ≤ |wordlist[i]| ≤ 7
```

**풀이**

사전을 미리 전처리 해 두면 `log(|wordlist|) * max({|querylist[i]|, |wordlist[i]|})` 에 비례하는 시간에 각 쿼리를 처리할 수 있다.

나는 실수로 처음에 모음을 소문자만 처리해 두고 왜 틀리지 ㅜㅜ 하면서 고생을 했는데, 규칙 순서를 잘 보면 대소문자를 없앤 이후에 모음 처리를 하면 되는거라 연산의 순서만 잘 맞췄어도 모음을 소문자만 고려해도 틀릴 이유가 없는 것이었다..

```cpp
#include<map>
using namespace std;
bool is_vowel[256]; // ascii code range

// delete vowel error
string delvo(const string & s) {
    string ret = s;
    for (int i = 0; i < s.sz; i++)
        if (is_vowel[s[i]])
            ret[i] = '_';
    return ret;
}

// delete capitalization error
string delca(const string & s) {
    string ret = s;
    for (int i = 0; i < s.sz; i++)
        ret[i] = tolower(ret[i]);
    return ret;
}

class Solution {
public:
    vector<string> spellchecker(vector<string>& wordlist, vector<string>& queries) {
        is_vowel['a'] = 1;
        is_vowel['e'] = 1;
        is_vowel['i'] = 1;
        is_vowel['o'] = 1;
        is_vowel['u'] = 1;
        map<string, string> ve, ce;
        // vowel error, capitalization error
        for (auto& w: wordlist) {
            auto && c = delca(w);
            auto && v = delvo(c);
            if (!ve.count(v))
                ve[v] = w;
            if (!ce.count(c))
                ce[c] = w;
        }
        sort(wordlist.begin(), wordlist.end());
        vector<string> ret;
        for (auto& q: queries) {
            if (binary_search(wordlist.begin(), wordlist.end(), q))
                ret.pb(q);
            else {
                auto && c = delca(q);
                auto && v = delvo(c);
                if (ce.count(c))
                    ret.pb(ce[c]);
                else if (ve.count(v))
                    ret.pb(ve[v]);
                else
                    ret.pb("");
            }
        }
        return ret;
    }
};
```

## Binary Tree Cameras

**문제**

이진트리의 일부 노드에 카메라를 설치해서 모든 노드를 감시하려고 한다.
각 카메라는 설치된 노드와, 직접 연결된 부모 노드와 자식 노드를 감시할 수 있다.
모든 노드를 감시하기 위해 필요한 최소 개수의 카메라는 몇 개일까?

**풀이**

매번 같은 말을 쓰는 것 같은데, 또 [유명한 동적 계획법 문제](https://courses.csail.mit.edu/6.006/spring11/lectures/lec21.pdf)이다.
사실 [그리디 접근이나 최대 유량을 응용한 방법](https://www.cl.cam.ac.uk/teaching/1415/AdvAlgo/lec8_ann.pdf)으로도 풀리는 것 같다.

우선 상태를 다음과 같이 정의하자:

1. `d[2][i]`: i번 노드에 카메라가 설치되어 있을 때, 해당 노드를 루트로 하는 서브트리에 설치해야 하는 카메라의 최소 개수
2. `d[1][i]`: i번 노드에 카메라는 없지만, 부모 노드에 카메라가 설치돼있는 경우, 해당 노드를 루트로 하는 서브트리에 설치해야 하는 카메라의 최소 개수
3. `d[0][i]`: i번 노드에 카메라가 없고, 부모 노드에도 카메라가 없는 경우, 해당 노드를 루트로 하는 서브트리에 설치해야 하는 카메라의 최소 개수

상태를 이렇게 정의하고 나면

- 1이나 2의 경우, 자식에 카메라를 설치하는 경우와 설치하지 않는 경우 모두를 보고 최솟값을 선택하면 된다.
- 3의 경우, 자식중 적어도 한 군데에는 무조건 카메라를 설치해야 한다.

처음에 3의 경우에 자식 모두에 카메라를 설치하는 만행을 저질렀다가 많이 틀렸다ㅜㅜ

```cpp
#include<map>
using namespace std;

map<int, int> d[3];

int camera(TreeNode * h, int cam)
{
    const int i = h -> val;
    if (d[cam].count(i)) return d[cam][i];
    int &ret = d[cam][i];
    ret = 1 << 30;
    if (!(h -> left) && !(h -> right))
        return ret = !cam;

    if (h -> left && !(h -> right)) {
        h -> left -> val = i * 2;
        ret = min(ret, camera(h -> left, 2) + 1);
        if (cam) ret = min(ret, camera(h -> left, cam - 1));
    }

    else if (h -> right && !(h -> left)) {
        h -> right -> val = i * 2 + 1;
        ret = min(ret, camera(h -> right, 2) + 1);
        if (cam) ret = min(ret, camera(h -> right, cam - 1));
    }

    else {
        h -> left -> val = i * 2;
        h -> right -> val = i * 2 + 1;
        int mini = max(0, cam - 1);
        ret = min(ret, camera(h -> left, 2) +  camera(h -> right, 2) + 2);
        ret = min(ret, camera(h -> left, mini) +  camera(h -> right, 2) + 1);
        ret = min(ret, camera(h -> left, 2) +  camera(h -> right, mini) + 1);
        if (cam) {
            ret = min(ret, camera(h -> right, cam - 1) + camera(h -> left, 2) + 1);
            ret = min(ret, camera(h -> right, 2) + camera(h -> left, cam - 1) + 1);
            ret = min(ret, camera(h -> right, cam - 1) + camera(h -> left, cam - 1));
        }
    }

    return ret;
}

/*
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};
 */

struct Solution {
    int minCameraCover(TreeNode* root) {
        d[0].clear();
        d[1].clear();
        d[2].clear();
        root -> val = 1;
        return min(camera(root, 2) + 1, camera(root, 0));
    }
};
```
