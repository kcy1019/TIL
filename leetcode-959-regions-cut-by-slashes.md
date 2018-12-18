# 959. Regions Cut By Slashes

[Problem Link](https://leetcode.com/contest/weekly-contest-115/problems/regions-cut-by-slashes/)

#### 문제

n x n 크기의 그리드가 주어진다.
그리드의 어떤 칸에는 `/`나 `\\` 모양의 벽이 존재한다.
이 그리드에는 벽으로 완전히 분리된 영역이 총 몇 개 존재할까?

#### 제한

* `0 ≤ n ≤ 30`

#### 풀이

<img src="https://raw.githubusercontent.com/kcy1019/TIL/images/leet-959.png" width="120px">

그리드의 한 칸을 이렇게 네 영역으로 자르면 단순한 [flood-fill][flood_fill] 문제가 된다.

```cpp
#include<string>
#include<array>
#include<queue>
using namespace std;

vector<string> grid;
vector<vector<array<bool, 4>>> visited;

void paint(int row, int col, int quad)
{
    visited[row][col][quad] = true;

    for (int ccw = 1; ccw <= 4; ccw++) {
        int nxt_quad = (quad + ccw) % 4;
        if (grid[row][col] == '/' && (quad + nxt_quad) != 3)
            continue;
        if (grid[row][col] == '\\' && (quad + nxt_quad) % 4 != 1)
            continue;
        if (!visited[row][col][nxt_quad])
            paint(row, col, nxt_quad);
    }

    int nxt_row = (row + (quad > 1 ? 1 : -1) * (quad % 2)),
        nxt_col = (col + (quad > 1 ? -1 : 1) * !(quad % 2));
    if (nxt_row >= 0 && nxt_col >= 0 &&
        nxt_row < grid.size() && nxt_col < grid[0].size() &&
        !visited[nxt_row][nxt_col][(quad + 2) % 4]) {
        paint(nxt_row, nxt_col, (quad + 2) % 4);
    }
}

struct Solution {
    int regionsBySlashes(vector<string>& _grid) {
        grid = move(_grid);
        visited.clear(), visited.resize(grid.size(), vector<array<bool, 4>>(grid[0].size()));
        int ans = 0;
        for (int row = 0; row < grid.size(); row++) {
            for (int col = 0; col < grid[row].size(); col++)
                for (int quad = 0; quad < 4; quad++) {
                    if (!visited[row][col][quad]) {
                        paint(row, col, quad);
                        ++ans;
                    }
                }
        }
        return ans;
    }
};
```

[flood_fill]: https://ko.wikipedia.org/wiki/%ED%94%8C%EB%9F%AC%EB%93%9C_%ED%95%84
