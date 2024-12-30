# 互斥 - Software Solution

<show-structure/>

四種針對進程互斥的軟體解決方案

## 單標誌法 {#singleFlag}
維護一個turn變數表示當前允許訪問臨界資源的進程
```c-like
int turn = 0;
```

<compare first-title="Process 1" second-title="Process 2">
    <code-block lang="c-like">
        while(turn != 0);
        printf("Using critical section");
        turn = 1;
        printf("remaining thing");
    </code-block>
    <code-block lang="c-like">
        while(turn != 1);
        printf("Using critical section");
        turn = 0;
        printf("remaining thing");
    </code-block>
</compare>

每次使用完臨界區間立即表明可以讓對方先用
但若**對方不訪問**臨界資源，此時P1也無法訪問，違反**空閒讓進(Mutual Exclusion)** 原則

## 雙標誌法 {#twoFlag}
### 雙標誌先檢查法 {#firstCheck}
維護一個 flag array，索引映射某個進程是否想要訪問臨界資源，初始狀態設為false
```c-like
bool flag[2];
flag[0] = false;
flag[1] = false;
```

<compare first-title="Process 1" second-title="Process 2">
    <code-block lang="C++">
        while(flag[1]); // 先檢查對方是否要用
        flag[0] = true;
        printf("Using critical section");
        flag[0] = false;
        printf("remaining thing");
    </code-block>
    <code-block lang="C++">
        while(flag[0]); // 先檢查對方是否要用
        flag[1] = true;
        printf("Using critical section");
        flag[1] = false;
        printf("remaining thing");
    </code-block>
</compare>

此方法檢查與上鎖的操作若並非原子操作，會發生問題

若P1上處理機，過了while迴圈，此時剛好發生切換，P2上處理機，則過後兩者會一起上鎖，違反**忙則等待(Busy Waiting)** 原則

### 雙標誌後檢查法 {#laterCheck}
與 [](#firstCheck) 相同，但是將檢查與上鎖的順序顛倒

<compare first-title="Process 1" second-title="Process 2">
    <code-block lang="C++">
        flag[0] = true;
        while(flag[1]);
        printf("Using critical section");
        flag[0] = false;
        printf("remaining thing");
    </code-block>
    <code-block lang="C++">
        flag[1] = true;
        while(flag[0]);
        printf("Using critical section");
        flag[1] = false;
        printf("remaining thing");
    </code-block>
</compare>

此方法滿足了**忙則等待(Busy Waiting)** 原則，但若考慮併發情況：  
P1先上處理機上鎖，但此時發生切換，P2上處理機後也上鎖，此時往後執行則兩者都無法訪問臨界資源  

違背了**空閒讓進(Mutual Exclusion)** 和**有限等待(Bounded Waiting)** 原則

## Peterson 算法
此方法結合 [雙標誌法](#firstCheck) 與 [單標誌法](#singleFlag) 的思想，維護flag array與turn變數
```c-like
bool flag[2];
int turn = 0;
```
<compare first-title="Process 1" second-title="Process 2" type="top-bottom">
    <code-block lang="C++">
        flag[0] = true; // 主動爭取
        turn = 1; // 謙讓
        while(flag[1] && turn == 1); // 檢查對方是否想用
        printf("Using critical section");
        flag[0] = false;
        printf("remaining thing");
    </code-block>
    <code-block lang="C++">
        flag[1] = true; // 主動爭取
        turn = 0; // 謙讓
        while(flag[0] && turn == 0); // 檢查對方是否想用
        printf("Using critical section");
        flag[1] = false;
        printf("remaining thing");
    </code-block>
</compare>

該算法滿足了前述的**空閒讓進(Mutual Exclusion)**、**有限等待(Bounded Waiting)**、**忙則等待(Busy Waiting)** 原則，相較於前三種算法是最好的一種

但是若某個Process進不了臨界區，仍然會在while迴圈持續執行，並沒有讓出處理機資源，違反**讓權等待(Blocking/Preemptive Waiting)** 原則