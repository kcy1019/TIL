---
layout: post
title:  "Google Code Jam 2020 Qualification Round - Indicium"
date:   2020-04-04 15:50:00 +0900
categories: ps
---

[올해 퀄 라운드의 마지막 문제][problem]는 [라틴 스퀘어][latin]를 만드는 문제였다.
라틴 스퀘어를 만드는 것 자체는 어렵지 않은 문제이지만,
이번에는 주 대각선의 합(trace)이 특정한 값이 되어야 한다는 조건이 있어서 상당히 어려웠다.

<style type="text/css">
#code {
  display: none;
}
#collapse-code {
  display: none;
}
#collapse-code:checked + * + #code {
  display: block !important;
}
#collapse-button {
    font-weight: bold;
    font-size: 13pt;
}
#collapse-code:not(:checked) ~ #collapse-button::after {
    content: " 펼치기";
}
#collapse-code:checked ~ #collapse-button::after {
    content: " 접기";
}
.asterisk {
    color: #F33;
    font-weight: bold;
}
.asterisk::after {
    content: "*";
}
</style>

1. toc
{:toc}

[<span style="font-size:18pt">문제 링크</span>][problem]<br>
<i class="asterisk"></i>라틴 스퀘어는 각 행과 열에 1..N 이 각각 한 번씩만 등장하는 N x N 행렬이다.

## 관찰

문제를 보고 자연스레 떠오르는 접근은 *"대각선을 먼저 채운 다음 나머지 칸을 채워보자"* 이다.
다만 일부가 채워져 있는 라틴 스퀘어를 완성하는 것은 일반적으로 NP-Complete[^1]에 속한다는 문제가 있다.
해법이 보이지 않으니 우선 Test Set 1 (2 ≤ n ≤ 5)을 해결하는 완전 탐색 코드를 작성하고 출력을 살펴보자.

### N+1, N<sup>2</sup>-1

N ≥ 4일 때의 출력을 살펴보면 trace가 N+1, N<sup>2</sup>-1 인 경우를 제외하고는 모두 라틴 스퀘어가 존재함을 알 수 있다.
trace = N+1 인 경우부터 살펴보면, 대각선이 다음과 같아서 마지막 행에 1을 둘 수 없으므로 완성이 불가능하다.

```
1
  1
   ...
      1
        2 <-- 1이 들어갈 자리는?
```

그리고 같은 이유로 trace = N<sup>2</sup>-1 인 경우에도 라틴 스퀘어가 존재하지 않는다.

```
N
  N
   ...
      N
        N-1 <-- N이 들어갈 자리는?
```

즉 1 ≤ x ≤ N 인 x가 대각선에 N-1번 등장할 경우 라틴 스퀘어를 완성할 수 없다.

### 그 외의 경우

그렇다면 나머지 경우에는 항상 라틴 스퀘어를 완성할 수 있을까?
일단 한 가지 수가 N-1번 등장하는 것은 피할 수 있다:

```
N+2   = 1, ..., 1, 1, 3  # x = 1, y = 3
      = 1, ..., 1, 2, 2

3N+2  = 3, ..., 3, 3, 5  # x = 3, y = 5
      = 3, ..., 3, 4, 4
      = 3, ..., 3, 2, 6

N^2-2 = N, ..., N, N,   N-2  # x = N, y = N-2
      = N, ..., N, N-1, N-1
```

1. N+1 &lt; K &lt; N<sup>2</sup>-1 인 K에 대해서
   K = (N-1)x + y (1 ≤ x, y ≤ N, x ≠ y) 라고 하자.
2. 조건으로부터 항상 y &lt; N-1 이거나 x &lt; N 임을 알 수 있고,
3. K = (N-3)x + (x-1) + (x-1) + (y+2) 혹은 K = (N-2)x + (x+1) + (y-1) 으로 다시 쓸 수 있다.
4. 따라서 같은 수가 N-1번 등장하는 표현을 N-2번 등장하는 표현으로 바꿀 수 있다.

그리고 대각선 위에 같은 수가 N-1번 등장하지 않는 경우 항상 라틴 사각형을 완성할 수 있다[^2].

<i class="asterisk"></i>물론 대회에서 선행 연구를 찾을 수 있는 것도 아니고, 이를 직접 증명하는 것은 무리이다.
실제로는 답이 존재할 경우 아래에서 설명할 알고리즘의 연산이 빠르게 완료된다는 사실을 이용,
몇 가지 크기에 대해 모든 입력을 직접 수행해보면서 확인했다.

## 라틴 스퀘어 채우기

라틴 스퀘어의 특징으로는 "라틴 스퀘어의 어느 두 행을 교환해도 라틴 스퀘어"라는 점이 있다.
여기서 직관적으로 라틴 스퀘어를 위에서부터 한 줄씩 만들어도 괜찮을 것이란 생각이 떠오른다[^3].

라틴 스퀘어의 각 행을 채우는 방법은 여러 가지가 있겠지만,
대회중에는 구현은 조금 길지만 아이디어가 간단한 [이분 매칭][bipartite]을 이용했다.
각 열에 대해 해당하는 칸에 들어갈 수 있는 수 모두와 간선을 이어주고 이분 매칭을 수행하면 끝!

예를 들어 5 x 5 에서 trace = 7 인 경우를 살펴보자.
<div class="highlighter-rouge" style="vertical-align:top;max-width:320px;display:inline-block;max-height:250px"><div class="highlight"><pre class="highlight">
<code>5 1 2 3 4  # 대각선은 5, 5, 1, 1, 1
1 5 3 4 2
2 4 1 5 3  # 여기까지 채운 상태
? ? ? 1 ?
? ? ? ? 5
</code></pre></div></div>

<svg viewbox="0 0 480 400" width="300" height="250" xmlns="http://www.w3.org/2000/svg">
 <g>
  <ellipse ry="36" rx="36" cy="49.419828" cx="113.5" stroke-width="1.5" stroke="#000" fill="none"/>
  <text stroke="#000" text-anchor="start" font-size="24" y="56.419828" x="86.5" stroke-width="0" fill="#000">(4, 1)</text>
  <ellipse ry="36" rx="36" cy="42.128191" cx="407.5" stroke-width="1.5" stroke="#000" fill="none"/>
  <text stroke="#000" text-anchor="start" font-size="24" y="48.128191" x="399.5" stroke-width="0" fill="#000">1</text>
  <ellipse ry="36" rx="36" cy="121.185129" cx="407.5" stroke-width="1.5" stroke="#000" fill="none"/>
  <text stroke="#000" text-anchor="start" font-size="24" y="129.185129" x="399.5" stroke-width="0" fill="#000">2</text>
  <ellipse ry="36" rx="36" cy="200.040117" cx="407.5" stroke-width="1.5" stroke="#000" fill="none"/>
  <text style="cursor: move;" stroke="#000" text-anchor="start" font-size="24" y="208.040117" x="399.5" stroke-width="0" fill="#000">3</text>
  <ellipse ry="36" rx="36" cy="279.160097" cx="407.5" stroke-width="1.5" stroke="#000" fill="none"/>
  <text stroke="#000" text-anchor="start" font-size="24" y="287.160097" x="399.5" stroke-width="0" fill="#000">4</text>
  <ellipse ry="36" rx="36" cy="358.659594" cx="407.5" stroke-width="1.5" stroke="#000" fill="none"/>
  <text stroke="#000" text-anchor="start" font-size="24" y="366.659594" x="399.5" stroke-width="0" fill="#000">5</text>
  <ellipse ry="36" rx="36" cy="125.426321" cx="113.5" stroke-width="1.5" stroke="#000" fill="none"/>
  <text stroke="#000" text-anchor="start" font-size="24" y="132.426321" x="86.5" stroke-width="0" fill="#000">(4, 2)</text>
  <ellipse ry="36" rx="36" cy="201.367537" cx="113.5" stroke-width="1.5" stroke="#000" fill="none"/>
  <text stroke="#000" text-anchor="start" font-size="24" y="208.367537" x="86.5" stroke-width="0" fill="#000">(4, 3)</text>
  <ellipse ry="36" rx="36" cy="277.132689" cx="113.5" stroke-width="1.5" stroke="#000" fill="none"/>
  <text stroke="#000" text-anchor="start" font-size="24" y="284.132689" x="86.5" stroke-width="0" fill="#000">(4, 4)</text>
  <ellipse ry="36" rx="36" cy="353.475737" cx="113.5" stroke-width="1.5" stroke="#000" fill="none"/>
  <text stroke="#000" text-anchor="start" font-size="24" y="360.475737" x="86.5" stroke-width="0" fill="#000">(4, 5)</text>
  <line y2="46.453125" x2="371.5" y1="273.453125" x1="149.5" stroke-width="1.5" stroke="#00f" fill="none"/>
  <line stroke="#000" y2="40.453125" x2="-683.5" y1="39.453125" x1="-683.5" stroke-width="1.5" fill="none"/>
  <line y2="281.453125" x2="371.5" y1="49.453125" x1="150.5" stroke-width="1.5" stroke="#000" fill="none"/>
  <line y2="200.453125" x2="372.5" y1="50.453125" x1="149.5" stroke-width="1.5" stroke="#000" fill="none"/>
  <line y2="359.453125" x2="372.5" y1="351.453125" x1="149.5" stroke-width="1.5" stroke="#000" fill="none"/>
  <line stroke="#000" y2="281.453125" x2="372.5" y1="201.453125" x1="149.5" stroke-width="1.5" fill="none"/>
  <line stroke="#000" y2="359.453125" x2="371.5" y1="201.453125" x1="149.5" stroke-width="1.5" fill="none"/>
  <line y2="123.453125" x2="371.5" y1="125.453125" x1="149.5" stroke-width="1.5" stroke="#000" fill="none"/>
  <line y2="201.453125" x2="371.5" y1="124.453125" x1="149.5" stroke-width="1.5" stroke="#000" fill="none"/>
 </g>
</svg>

위와 같이 3번째 행까지 채워져 있는 상태에서 4번째 행의 각 열을 왼쪽부터 (4,1), (4,2), ... 로 표현하면
4번째 행을 채우기 위한 이분 그래프는 다음과 같이 구성된다:

- (4,1)은 첫 번째 열에 1, 2, 5가 올 수 없으므로 3, 4와 연결
- (4,2)는 두 번째 열에 1, 4, 5가 올 수 없으므로 2, 3과 연결
- (4,3)은 세 번째 열에 1, 2, 3이 올 수 없으므로 4, 5와 연결
- (4,4)는 대각선 위의 칸이므로 1과 연결
- (4,5)는 다섯 번째 열에 2, 3,4가 올 수 없으며 1은 (4,4)에 들어가야 하므로 5와 연결

이렇게 만든 그래프에 이분 매칭을 수행하여 행을 완성할 수 있고, 이를 반복하면 라틴 스퀘어가 완성된다.

### 대각선 찾기

```
size = 5, trace = 7

diagonal: 1,1,1,2,2    2,2,1,1,1
  result: FAIL         SUCCESS
          1 2 3 4 5    2 1 3 4 5
          2 1 4 5 3    1 2 4 5 3
          5 4 1 3 ?    3 5 1 2 4
                2      4 3 5 1 2
                  2    5 4 2 3 1
```

사실 대각선을 미리 채워둔 상태에서는 단순히 위에서부터 한 행씩 그리디하게 채우는 방법으로는 채우지 못하는 경우가 존재한다.
하지만 이런 경우에도 대각선 위 원소들의 순서만 바꾸면 해결할 수 있고,
대부분의 순서에 대해서는 이 방법을 통해 문제 없이 채울 수 있으므로 대각선을 채우는 부분은 완전 탐색으로 구현했다.

<div style="margin-bottom:1%">
<input type="checkbox" id="collapse-code" />
<label id="collapse-button" for="collapse-code">코드</label>
<div id="code" class="language-cpp highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="cp">#include&lt;cstdio&gt;
#include&lt;set&gt;
#include&lt;queue&gt;
</span><span class="k">using</span> <span class="k">namespace</span> <span class="n">std</span><span class="p">;</span>

<span class="kt">int</span> <span class="n">n</span><span class="p">,</span> <span class="n">trace</span><span class="p">;</span>
<span class="n">vector</span><span class="o">&lt;</span><span class="n">vector</span><span class="o">&lt;</span><span class="kt">int</span><span class="o">&gt;&gt;</span> <span class="n">square</span><span class="p">;</span>
<span class="n">vector</span><span class="o">&lt;</span><span class="n">set</span><span class="o">&lt;</span><span class="kt">int</span><span class="o">&gt;&gt;</span> <span class="n">row_missing</span><span class="p">;</span>
<span class="n">vector</span><span class="o">&lt;</span><span class="n">set</span><span class="o">&lt;</span><span class="kt">int</span><span class="o">&gt;&gt;</span> <span class="n">col_missing</span><span class="p">;</span>

<span class="kt">void</span> <span class="nf">print_square</span><span class="p">()</span> <span class="p">{</span>
    <span class="k">for</span> <span class="p">(</span><span class="kt">int</span> <span class="n">i</span> <span class="o">=</span> <span class="mi">1</span><span class="p">;</span> <span class="n">i</span> <span class="o">&lt;=</span> <span class="n">n</span><span class="p">;</span> <span class="n">i</span><span class="o">++</span><span class="p">)</span> <span class="p">{</span>
        <span class="k">for</span> <span class="p">(</span><span class="kt">int</span> <span class="n">j</span> <span class="o">=</span> <span class="mi">1</span><span class="p">;</span> <span class="n">j</span> <span class="o">&lt;=</span> <span class="n">n</span><span class="p">;</span> <span class="n">j</span><span class="o">++</span><span class="p">)</span> <span class="p">{</span>
            <span class="n">printf</span><span class="p">(</span><span class="s">"%d "</span><span class="p">,</span> <span class="n">square</span><span class="p">[</span><span class="n">i</span><span class="p">][</span><span class="n">j</span><span class="p">]);</span>
        <span class="p">}</span>
        <span class="n">puts</span><span class="p">(</span><span class="s">""</span><span class="p">);</span>
    <span class="p">}</span>
<span class="p">}</span>

<span class="n">vector</span><span class="o">&lt;</span><span class="n">vector</span><span class="o">&lt;</span><span class="kt">int</span><span class="o">&gt;&gt;</span> <span class="n">graph</span><span class="p">;</span>
<span class="n">vector</span><span class="o">&lt;</span><span class="kt">int</span><span class="o">&gt;</span> <span class="n">row_match</span><span class="p">,</span> <span class="n">col_match</span><span class="p">;</span>

<span class="kt">bool</span> <span class="nf">find_match</span><span class="p">(</span><span class="kt">int</span> <span class="n">source</span><span class="p">)</span>
<span class="p">{</span>
    <span class="n">vector</span><span class="o">&lt;</span><span class="kt">int</span><span class="o">&gt;</span> <span class="n">from</span><span class="p">(</span><span class="n">row_match</span><span class="p">.</span><span class="n">size</span><span class="p">(),</span> <span class="o">-</span><span class="mi">1</span><span class="p">);</span>
    <span class="kt">int</span> <span class="n">now</span><span class="p">,</span> <span class="n">match</span><span class="p">;</span>
    <span class="n">from</span><span class="p">[</span><span class="n">source</span><span class="p">]</span> <span class="o">=</span> <span class="n">source</span><span class="p">;</span>
    <span class="n">queue</span><span class="o">&lt;</span><span class="kt">int</span><span class="o">&gt;</span> <span class="n">q</span><span class="p">;</span>
    <span class="n">q</span><span class="p">.</span><span class="n">emplace</span><span class="p">(</span><span class="n">source</span><span class="p">);</span>
    <span class="kt">bool</span> <span class="n">found_path</span> <span class="o">=</span> <span class="nb">false</span><span class="p">;</span>
    <span class="k">while</span> <span class="p">(</span><span class="o">!</span><span class="n">found_path</span> <span class="o">&amp;&amp;</span> <span class="o">!</span><span class="n">q</span><span class="p">.</span><span class="n">empty</span><span class="p">())</span> <span class="p">{</span>
        <span class="n">now</span> <span class="o">=</span> <span class="n">q</span><span class="p">.</span><span class="n">front</span><span class="p">();</span> <span class="n">q</span><span class="p">.</span><span class="n">pop</span><span class="p">();</span>
        <span class="k">for</span> <span class="p">(</span><span class="kt">int</span> <span class="n">i</span><span class="o">:</span> <span class="n">graph</span><span class="p">[</span><span class="n">now</span><span class="p">])</span> <span class="p">{</span>
            <span class="n">match</span> <span class="o">=</span> <span class="n">i</span><span class="p">;</span>
            <span class="kt">int</span> <span class="n">next</span> <span class="o">=</span> <span class="n">col_match</span><span class="p">[</span><span class="n">match</span><span class="p">];</span>
            <span class="k">if</span> <span class="p">(</span><span class="n">now</span> <span class="o">!=</span> <span class="n">next</span><span class="p">) {</span>
                <span class="k">if</span> <span class="p">(</span><span class="n">next</span> <span class="o">==</span> <span class="o">-</span><span class="mi">1</span><span class="p">) {</span>
                    <span class="n">found_path</span> <span class="o">=</span> <span class="nb">true</span><span class="p">;</span>
                    <span class="k">break</span><span class="p">;</span>
                <span class="p">}</span>
                <span class="k">if</span> <span class="p">(</span><span class="n">from</span><span class="p">[</span><span class="n">next</span><span class="p">]</span> <span class="o">==</span> <span class="o">-</span><span class="mi">1</span><span class="p">) {</span>
                    <span class="n">q</span><span class="p">.</span><span class="n">emplace</span><span class="p">(</span><span class="n">next</span><span class="p">);</span>
                    <span class="n">from</span><span class="p">[</span><span class="n">next</span><span class="p">]</span> <span class="o">=</span> <span class="n">now</span><span class="p">;</span>
                <span class="p">}</span>
            <span class="p">}</span>
        <span class="p">}</span>
    <span class="p">}</span>

    <span class="k">if</span> <span class="p">(</span><span class="o">!</span><span class="n">found_path</span><span class="p">)</span> <span class="k">return</span> <span class="nb">false</span><span class="p">;</span>

    <span class="k">while</span> <span class="p">(</span><span class="n">from</span><span class="p">[</span><span class="n">now</span><span class="p">]</span> <span class="o">!=</span> <span class="n">now</span><span class="p">)</span> <span class="p">{</span>
        <span class="kt">int</span> <span class="n">aux</span> <span class="o">=</span> <span class="n">row_match</span><span class="p">[</span><span class="n">now</span><span class="p">];</span>
        <span class="n">row_match</span><span class="p">[</span><span class="n">now</span><span class="p">]</span> <span class="o">=</span> <span class="n">match</span><span class="p">;</span>
        <span class="n">col_match</span><span class="p">[</span><span class="n">match</span><span class="p">]</span> <span class="o">=</span> <span class="n">now</span><span class="p">;</span>
        <span class="n">now</span> <span class="o">=</span> <span class="n">from</span><span class="p">[</span><span class="n">now</span><span class="p">];</span>
        <span class="n">match</span> <span class="o">=</span> <span class="n">aux</span><span class="p">;</span>
    <span class="p">}</span>

    <span class="n">row_match</span><span class="p">[</span><span class="n">now</span><span class="p">]</span> <span class="o">=</span> <span class="n">match</span><span class="p">;</span>
    <span class="n">col_match</span><span class="p">[</span><span class="n">match</span><span class="p">]</span> <span class="o">=</span> <span class="n">now</span><span class="p">;</span>
    <span class="k">return</span> <span class="nb">true</span><span class="p">;</span>
<span class="p">}</span>

<span class="kt">void</span> <span class="nf">fill_row</span><span class="p">(</span><span class="kt">int</span> <span class="n">i</span><span class="p">)</span> <span class="p">{</span>
    <span class="n">graph</span><span class="p">.</span><span class="n">clear</span><span class="p">();</span>
    <span class="n">row_match</span><span class="p">.</span><span class="n">clear</span><span class="p">();</span>
    <span class="n">col_match</span><span class="p">.</span><span class="n">clear</span><span class="p">();</span>

    <span class="n">graph</span><span class="p">.</span><span class="n">resize</span><span class="p">(</span><span class="n">n</span> <span class="o">+</span> <span class="mi">10</span><span class="p">);</span>
    <span class="n">row_match</span><span class="p">.</span><span class="n">resize</span><span class="p">(</span><span class="n">n</span> <span class="o">+</span> <span class="mi">10</span><span class="p">,</span> <span class="o">-</span><span class="mi">1</span><span class="p">);</span>
    <span class="n">col_match</span><span class="p">.</span><span class="n">resize</span><span class="p">(</span><span class="n">n</span> <span class="o">+</span> <span class="mi">10</span><span class="p">,</span> <span class="o">-</span><span class="mi">1</span><span class="p">);</span>

    <span class="k">for</span> <span class="p">(</span><span class="kt">int</span> <span class="n">j</span> <span class="o">=</span> <span class="mi">1</span><span class="p">;</span> <span class="n">j</span> <span class="o">&lt;=</span> <span class="n">n</span><span class="p">;</span> <span class="n">j</span><span class="o">++</span><span class="p">)</span> <span class="p">{</span>
        <span class="k">if</span> <span class="p">(</span><span class="n">square</span><span class="p">[</span><span class="n">i</span><span class="p">][</span><span class="n">j</span><span class="p">])</span> <span class="k">continue</span><span class="p">;</span>

        <span class="k">for</span> <span class="p">(</span><span class="kt">int</span> <span class="n">k</span> <span class="o">=</span> <span class="mi">1</span><span class="p">;</span> <span class="n">k</span> <span class="o">&lt;=</span> <span class="n">n</span><span class="p">;</span> <span class="n">k</span><span class="o">++</span><span class="p">)</span> <span class="p">{</span>
            <span class="k">if</span> <span class="p">(</span><span class="o">!</span><span class="n">row_missing</span><span class="p">[</span><span class="n">i</span><span class="p">].</span><span class="n">count</span><span class="p">(</span><span class="n">k</span><span class="p">))</span> <span class="k">continue</span><span class="p">;</span>
            <span class="k">if</span> <span class="p">(</span><span class="o">!</span><span class="n">col_missing</span><span class="p">[</span><span class="n">j</span><span class="p">].</span><span class="n">count</span><span class="p">(</span><span class="n">k</span><span class="p">))</span> <span class="k">continue</span><span class="p">;</span>

            <span class="n">graph</span><span class="p">[</span><span class="n">j</span><span class="p">].</span><span class="n">emplace_back</span><span class="p">(</span><span class="n">k</span><span class="p">);</span>
        <span class="p">}</span>
    <span class="p">}</span>

    <span class="k">for</span> <span class="p">(</span><span class="kt">int</span> <span class="n">j</span> <span class="o">=</span> <span class="mi">1</span><span class="p">;</span> <span class="n">j</span> <span class="o">&lt;=</span> <span class="n">n</span><span class="p">;</span> <span class="n">j</span><span class="o">++</span><span class="p">)</span> <span class="p">{</span>
        <span class="k">if</span> <span class="p">(</span><span class="n">square</span><span class="p">[</span><span class="n">i</span><span class="p">][</span><span class="n">j</span><span class="p">])</span> <span class="k">continue</span><span class="p">;</span>
        <span class="k">if</span> <span class="p">(</span><span class="o">!</span><span class="n">find_match</span><span class="p">(</span><span class="n">j</span><span class="p">))</span> <span class="k">throw</span> <span class="mi">0</span><span class="p">;</span>
    <span class="p">}</span>

    <span class="k">for</span> <span class="p">(</span><span class="kt">int</span> <span class="n">j</span> <span class="o">=</span> <span class="mi">1</span><span class="p">;</span> <span class="n">j</span> <span class="o">&lt;=</span> <span class="n">n</span><span class="p">;</span> <span class="n">j</span><span class="o">++</span><span class="p">)</span> <span class="p">{</span>
        <span class="k">if</span> <span class="p">(</span><span class="n">square</span><span class="p">[</span><span class="n">i</span><span class="p">][</span><span class="n">j</span><span class="p">])</span> <span class="k">continue</span><span class="p">;</span>
        <span class="n">square</span><span class="p">[</span><span class="n">i</span><span class="p">][</span><span class="n">j</span><span class="p">]</span> <span class="o">=</span> <span class="n">row_match</span><span class="p">[</span><span class="n">j</span><span class="p">];</span>
    <span class="p">}</span>
<span class="p">}</span>

<span class="kt">bool</span> <span class="nf">fill_square</span><span class="p">()</span>
<span class="p">{</span>
    <span class="k">auto</span> <span class="n">bak_square</span> <span class="o">=</span> <span class="n">square</span><span class="p">;</span>
    <span class="k">auto</span> <span class="n">bak_row_missing</span> <span class="o">=</span> <span class="n">row_missing</span><span class="p">;</span>
    <span class="k">auto</span> <span class="n">bak_col_missing</span> <span class="o">=</span> <span class="n">col_missing</span><span class="p">;</span>

    <span class="k">try</span> <span class="p">{</span>
        <span class="k">for</span> <span class="p">(</span><span class="kt">int</span> <span class="n">i</span> <span class="o">=</span> <span class="mi">1</span><span class="p">;</span> <span class="n">i</span> <span class="o">&lt;=</span> <span class="n">n</span><span class="p">;</span> <span class="n">i</span><span class="o">++</span><span class="p">)</span> <span class="p">{</span>
            <span class="n">fill_row</span><span class="p">(</span><span class="n">i</span><span class="p">);</span>
            <span class="k">for</span> <span class="p">(</span><span class="kt">int</span> <span class="n">j</span> <span class="o">=</span> <span class="mi">1</span><span class="p">;</span> <span class="n">j</span> <span class="o">&lt;=</span> <span class="n">n</span><span class="p">;</span> <span class="n">j</span><span class="o">++</span><span class="p">)</span> <span class="p">{</span>
                <span class="n">col_missing</span><span class="p">[</span><span class="n">j</span><span class="p">].</span><span class="n">erase</span><span class="p">(</span><span class="n">square</span><span class="p">[</span><span class="n">i</span><span class="p">][</span><span class="n">j</span><span class="p">]);</span>
                <span class="n">row_missing</span><span class="p">[</span><span class="n">i</span><span class="p">].</span><span class="n">erase</span><span class="p">(</span><span class="n">square</span><span class="p">[</span><span class="n">i</span><span class="p">][</span><span class="n">j</span><span class="p">]);</span>
            <span class="p">}</span>
        <span class="p">}</span>
        <span class="k">return</span> <span class="nb">true</span><span class="p">;</span>
    <span class="p">}</span> <span class="k">catch</span> <span class="p">(...)</span> <span class="p">{</span>
        <span class="n">square</span> <span class="o">=</span> <span class="n">bak_square</span><span class="p">;</span>
        <span class="n">row_missing</span> <span class="o">=</span> <span class="n">bak_row_missing</span><span class="p">;</span>
        <span class="n">col_missing</span> <span class="o">=</span> <span class="n">bak_col_missing</span><span class="p">;</span>
        <span class="k">return</span> <span class="nb">false</span><span class="p">;</span>
    <span class="p">}</span>
<span class="p">}</span>

<span class="kt">bool</span> <span class="nf">fill_diag</span><span class="p">(</span><span class="kt">int</span> <span class="n">i</span><span class="p">,</span> <span class="kt">int</span> <span class="n">sum</span><span class="p">)</span>
<span class="p">{</span>
    <span class="k">if</span> <span class="p">(</span><span class="n">i</span> <span class="o">&gt;</span> <span class="n">n</span><span class="p">)</span> <span class="k">return</span> <span class="n">sum</span> <span class="o">==</span> <span class="n">trace</span> <span class="o">&amp;&amp;</span> <span class="n">fill_square</span><span class="p">();</span>

    <span class="k">for</span> <span class="p">(</span><span class="kt">int</span> <span class="n">k</span> <span class="o">=</span> <span class="n">min</span><span class="p">(</span><span class="n">n</span><span class="p">,</span> <span class="n">trace</span><span class="o">-</span><span class="n">sum</span><span class="o">-</span><span class="p">(</span><span class="n">n</span><span class="o">-</span><span class="n">i</span><span class="p">));</span> <span class="n">k</span> <span class="o">&gt;</span> <span class="mi">0</span><span class="p">;</span> <span class="o">--</span><span class="n">k</span><span class="p">)</span> <span class="p">{</span>
        <span class="k">if</span> <span class="p">(</span><span class="o">!</span><span class="n">row_missing</span><span class="p">[</span><span class="n">i</span><span class="p">].</span><span class="n">count</span><span class="p">(</span><span class="n">k</span><span class="p">))</span> <span class="k">continue</span><span class="p">;</span>
        <span class="k">if</span> <span class="p">(</span><span class="o">!</span><span class="n">col_missing</span><span class="p">[</span><span class="n">i</span><span class="p">].</span><span class="n">count</span><span class="p">(</span><span class="n">k</span><span class="p">))</span> <span class="k">continue</span><span class="p">;</span>

        <span class="kt">int</span> <span class="n">nsum</span> <span class="o">=</span> <span class="n">sum</span> <span class="o">+</span> <span class="n">k</span><span class="p">;</span>
        <span class="n">row_missing</span><span class="p">[</span><span class="n">i</span><span class="p">].</span><span class="n">erase</span><span class="p">(</span><span class="n">k</span><span class="p">);</span>
        <span class="n">col_missing</span><span class="p">[</span><span class="n">i</span><span class="p">].</span><span class="n">erase</span><span class="p">(</span><span class="n">k</span><span class="p">);</span>
        <span class="n">square</span><span class="p">[</span><span class="n">i</span><span class="p">][</span><span class="n">i</span><span class="p">]</span> <span class="o">=</span> <span class="n">k</span><span class="p">;</span>
        <span class="k">if</span> <span class="p">(</span><span class="n">fill_diag</span><span class="p">(</span><span class="n">i</span> <span class="o">+</span> <span class="mi">1</span><span class="p">,</span> <span class="n">nsum</span><span class="p">))</span> <span class="k">return</span> <span class="nb">true</span><span class="p">;</span>
        <span class="n">square</span><span class="p">[</span><span class="n">i</span><span class="p">][</span><span class="n">i</span><span class="p">]</span> <span class="o">=</span> <span class="mi">0</span><span class="p">;</span>
        <span class="n">col_missing</span><span class="p">[</span><span class="n">i</span><span class="p">].</span><span class="n">emplace</span><span class="p">(</span><span class="n">k</span><span class="p">);</span>
        <span class="n">row_missing</span><span class="p">[</span><span class="n">i</span><span class="p">].</span><span class="n">emplace</span><span class="p">(</span><span class="n">k</span><span class="p">);</span>
    <span class="p">}</span>

    <span class="k">return</span> <span class="nb">false</span><span class="p">;</span>
<span class="p">}</span>

<span class="kt">void</span> <span class="nf">build_latin_square</span><span class="p">()</span>
<span class="p">{</span>
    <span class="k">if</span> <span class="p">(</span><span class="n">trace</span> <span class="o">==</span> <span class="n">n</span> <span class="o">*</span> <span class="n">n</span> <span class="o">-</span> <span class="mi">1</span> <span class="o">||</span> <span class="n">trace</span> <span class="o">==</span> <span class="n">n</span> <span class="o">*</span> <span class="n">n</span> <span class="o">+</span> <span class="mi">1</span><span class="p">)</span> <span class="k">throw</span> <span class="mi">0</span><span class="p">;</span>

    <span class="n">square</span><span class="p">.</span><span class="n">clear</span><span class="p">();</span>
    <span class="n">row_missing</span><span class="p">.</span><span class="n">clear</span><span class="p">();</span>
    <span class="n">col_missing</span><span class="p">.</span><span class="n">clear</span><span class="p">();</span>

    <span class="n">square</span><span class="p">.</span><span class="n">resize</span><span class="p">(</span><span class="n">n</span><span class="o">+</span><span class="mi">1</span><span class="p">,</span> <span class="n">vector</span><span class="o">&lt;</span><span class="kt">int</span><span class="o">&gt;</span><span class="p">(</span><span class="n">n</span><span class="o">+</span><span class="mi">1</span><span class="p">,</span> <span class="mi">0</span><span class="p">));</span>
    <span class="n">row_missing</span><span class="p">.</span><span class="n">resize</span><span class="p">(</span><span class="n">n</span><span class="o">+</span><span class="mi">1</span><span class="p">);</span>
    <span class="n">col_missing</span><span class="p">.</span><span class="n">resize</span><span class="p">(</span><span class="n">n</span><span class="o">+</span><span class="mi">1</span><span class="p">);</span>

    <span class="k">for</span> <span class="p">(</span><span class="kt">int</span> <span class="n">i</span> <span class="o">=</span> <span class="mi">1</span><span class="p">;</span> <span class="n">i</span> <span class="o">&lt;=</span> <span class="n">n</span><span class="p">;</span> <span class="n">i</span><span class="o">++</span><span class="p">)</span> <span class="p">{</span>
        <span class="k">for</span> <span class="p">(</span><span class="kt">int</span> <span class="n">j</span> <span class="o">=</span> <span class="mi">1</span><span class="p">;</span> <span class="n">j</span> <span class="o">&lt;=</span> <span class="n">n</span><span class="p">;</span> <span class="n">j</span><span class="o">++</span><span class="p">)</span> <span class="p">{</span>
            <span class="n">row_missing</span><span class="p">[</span><span class="n">i</span><span class="p">].</span><span class="n">emplace</span><span class="p">(</span><span class="n">j</span><span class="p">);</span>
            <span class="n">col_missing</span><span class="p">[</span><span class="n">i</span><span class="p">].</span><span class="n">emplace</span><span class="p">(</span><span class="n">j</span><span class="p">);</span>
        <span class="p">}</span>
    <span class="p">}</span>

    <span class="k">if</span> <span class="p">(</span><span class="o">!</span><span class="n">fill_diag</span><span class="p">(</span><span class="mi">1</span><span class="p">,</span> <span class="mi">0</span><span class="p">))</span> <span class="k">throw</span> <span class="mi">0</span><span class="p">;</span>
<span class="p">}</span>

<span class="kt">int</span> <span class="nf">main</span><span class="p">()</span>
<span class="p">{</span>
    <span class="kt">int</span> <span class="n">T</span><span class="p">;</span>
    <span class="k">for</span> <span class="p">(</span><span class="kt">int</span> <span class="n">C</span> <span class="o">=</span> <span class="n">scanf</span><span class="p">(</span><span class="s">"%d"</span><span class="p">,</span> <span class="o">&amp;</span><span class="n">T</span><span class="p">);</span> <span class="n">T</span><span class="o">--</span><span class="p">;</span> <span class="n">C</span><span class="o">++</span><span class="p">)</span> <span class="p">{</span>
        <span class="n">printf</span><span class="p">(</span><span class="s">"Case #%d: "</span><span class="p">,</span> <span class="n">C</span><span class="p">);</span>
        <span class="n">scanf</span><span class="p">(</span><span class="s">"%d%d"</span><span class="p">,</span> <span class="o">&amp;</span><span class="n">n</span><span class="p">,</span> <span class="o">&amp;</span><span class="n">trace</span><span class="p">);</span>
        <span class="k">try</span> <span class="p">{</span>
            <span class="n">build_latin_square</span><span class="p">();</span>
            <span class="n">puts</span><span class="p">(</span><span class="s">"POSSIBLE"</span><span class="p">);</span>
            <span class="n">print_square</span><span class="p">();</span>
        <span class="p">}</span> <span class="k">catch</span> <span class="p">(...)</span> <span class="p">{</span>
            <span class="n">puts</span><span class="p">(</span><span class="s">"IMPOSSIBLE"</span><span class="p">);</span>
        <span class="p">}</span>
    <span class="p">}</span>
    <span class="k">return</span> <span class="mi">0</span><span class="p">;</span>
<span class="p">}</span>
</code></pre></div></div>
</div>

### 다른 방법

사실 이 문제는 케이스를 잘 나누고 규칙에 따라 채우는 방법[[4]][silver][[5]][solution]으로도 깔끔하게 해결할 수 있다.

[^1]: [Charles J. Colbourn, The complexity of completing partial Latin squares, Discrete Applied Mathematics (1984)][np-complete]
[^2]: [Chang, G., Complete Diagonals of Latin Squares, Canadian Mathematical Bulletin (1979)][chang]
[^3]: [Every Latin rectangle can be completed to a Latin square.][hall]

[problem]: https://codingcompetitions.withgoogle.com/codejam/round/000000000019fd27/0000000000209aa0
[np-complete]: https://www.sciencedirect.com/science/article/pii/0166218X84900751
[latin]: https://mathworld.wolfram.com/LatinSquare.html
[chang]: https://www.cambridge.org/core/journals/canadian-mathematical-bulletin/article/complete-diagonals-of-latin-squares/D451C2989FFA7FF4DB2A87128A3C6B45
[hall]: http://web.math.ucsb.edu/~padraic/mathcamp_2012/latin_squares/MC2012_LatinSquares_lecture1.pdf
[solution]: https://hackmd.io/@tatyam-prime/indicium
[bipartite]: https://blog.naver.com/kks227/220807541506
[silver]: https://twitter.com/16silver_t/status/1246618720241238017
