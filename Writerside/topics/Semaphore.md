# 信號量

<show-structure/>

## 機制
信號量是一個**變數**，通常用來表示**系統中某種資源的數量**(也可以是更複雜的紀錄型變數)

OS為我們提供了一對**原子方法**
- wait(S)
- signal(S)

通常會被簡稱為P、V操作，源自荷蘭語`proberen`和`verhogen`

因此兩個方法可以被簡寫為 **P(S) 、 V(S)**

## 整型信號量
這種信號量是整數型，用來表示系統中某資源的數量

對整型信號量只能做三種操作

<list>
<li>
初始化
<code-block lang="c++">
// 初始化整型信號量，表示可用印表機數量
int S = 1;
</code-block>
</li>
<li>
P操作
<code-block lang="C++">
// wait方法，進入臨界區前須調用
void wait(int S) {
    while(S &lt;= 0);
    S = S - 1;
}
</code-block>
</li>
<li>
V操作
<code-block lang="c++">
// signal方法，退出臨界區前須調用
void signal(int S) {
    S = S + 1;
}
</code-block>
</li>
</list>


進程可以調用P、V操作以存取臨界資源
<tabs>
<tab title="Process N">
<code-block lang="c++">
...
wait(S);                    //申請資源
printf("using printer");    //訪問資源
signal(S);                  //釋放資源
...
</code-block>
</tab>
</tabs>

<tip>

[](ExclusiveSoftware.md#firstCheck) 與整型信號量的邏輯是一樣的
</tip>

整型信號量存在的問題是會造成**忙等**，不滿足**讓權等待(Blocking/Preemptive Waiting)** 原則

## 記錄型信號量

為了解決整型信號量的**忙等**問題，我們可以利用紀錄型資料結構表示的信號量


<list>
<li>
定義記錄型信號量
<code-block lang="c">
typedef struct {
    int value;
    struct process *L; // waiting queue
} semaphore;
</code-block>
</li>
<li>
P操作
<code-block lang="C++">
// wait方法，申請資源
void wait(semaphore S) {
    S.value--;
    if(S.value &lt; 0) {
        // 使進程進入阻塞態
        // 並掛到S的waiting queue
        block(S.L);
    }
}
</code-block>
</li>
<li>
V操作
<code-block lang="c++">
// signal方法，釋放資源
void signal(semaphore S) {
    S.value++;
    if(S.value &lt;= 0) {
        // 喚醒waiting queue中的一個進程
        wakeup(S.L);
    }
}
</code-block>
</li>
</list>

舉個例子

一個系統中有兩個印表機可用

<code-block lang="D2">
vars: {
    d2-config: {
        theme-id: 200
    }
}
direction: down
Semaphore : {
    value: value: 2
    waitingQueue: []
}
</code-block>

此時 P1 進程上處理機要求資源

<code-block lang="d2">
vars: {
    d2-config: {
        theme-id: 200
    }
}
direction: right
P1: Process 1
P1 -> wait(S) -> Semaphore.value=1
</code-block>

P2 上處理機要求資源
<code-block lang="d2">
vars: {
    d2-config: {
        theme-id: 200
    }
}
direction: right
P2: Process 2
P2 -> wait(S) -> Semaphore.value=0
</code-block>

此時 P3 上處理機，因為value = 0 資源恰好分配完，所以會進入 wait 方法的 if 語句而被阻塞
<code-block lang="d2">
vars: {
    d2-config: {
        theme-id: 200
    }
}
direction: right
P3: Process 3
P3 -> wait(S) -> block -- Semaphore
Semaphore.value = -1
Semaphore.waitingQueue = (P3,)
</code-block>
value = -1 時代表有 1 個進程正在等待(取絕對值)

而如果此時處理機回去處理P1，P1執行 signal(S)，就會釋放一個印表機資源，接著waiting queue隊首的process就能夠使用印表機資源

<code-block lang="d2">
vars: {
    d2-config: {
        theme-id: 200
    }
}
direction: right
P1: Process 1
P1 -> signal(S) -> Semaphore.value=0
signal(S) -> wakeup(S-waitingQueue)
wakeup(S-waitingQueue) -> Semaphore.waitingQueue = ()
</code-block>

因為這種機制在無法進入臨界區時會調用block自我阻塞，主動放棄CPU，所以滿足了**讓權等待(Blocking/Preemptive Waiting)** 原則