# 互斥 - Hardware Solution

<show-structure/>

三種針對進程互斥的硬體解決方案

## 中斷屏蔽方法
利用**開/關中斷**來實現不可被中斷

當執行了**關中斷**之後，就不允許當前Process被中斷(不會發生切換)

直到訪問完臨界資源再**開中斷**

Assembly Example (8086 style)
```ARMASM
DATA_SEG SEGMENT
    SHARED_RESOURCE DW 0    ;臨界資源
DATA_SEG ENDS

CODE_SEG SEGMENT
CLI                         ;關中斷

MOV AX, SHARED_RESOURCE     ;訪問臨界資源
ADD AX, 1
MOV SHARED_RESOURCE, AX

STI                         ;開中斷
CODE_SEG ENDS

```

<tabs>
<tab title="優點">
簡單且高效
</tab>
<tab title="缺點">
不適用於多處理機；只適用於作業系統 Kernel Process

不適用於使用者進程 <br/> (開/關中斷操作只能在 Kernel Mode 執行)
</tab>
</tabs>


## TestAndSet
簡稱稱TS指令、又稱TSL(TestAndSetLock)指令

TSL指令都是使用硬體實現的，是原子操作，執行的過程中不允許被中斷

下面的程式碼描述TSL指令的實現
```C++
bool TestAndSet(bool *lock) {
    bool old;
    old = *lock;    // 先儲存lock原來的值
    *lock = true;   // 一律上鎖
    return old;     // 回傳lock原來的值
}
```
於是Process就可以使用TSL指令以達成**檢查**與**上鎖**不被中斷
```C++
while(TestAndSet(&lock)); // 上鎖並檢查
printf("Using critical section");
lock = false;
printf("remaining thing");
```

{#TSLAD}
<tabs>
<tab title="優點">
<list>
    <li>
    實現簡單
    <list>
        <li>
        不需如軟體方案那樣嚴格檢查邏輯漏洞
        </li>
    </list>
    </li>
    <li>
    適用多處理機環境
    </li>
</list>
</tab>
<tab title="缺點">

- 不滿足**讓權等待(Blocking/Preemptive Waiting)** 原則
  - 無法訪問臨界資源之進程仍然會占用CPU持續忙等
</tab>
</tabs>

## Swap
又稱XCHG指令  
也是用硬體實現，過程不允許被中斷

下面的程式碼描述XCHG指令的實現
```C++
void swap(bool *a, bool *b) {
    bool temp;
    temp = *a;
    *a = *b;
    *b = temp;
}
```
於是 Process 就可以使用Swap指令實現互斥的算法邏輯
```c++
bool old = true;
while(old == true){
    swap(&lock, &old);
}
printf("Using critical section");
lock = false;
printf("remaining thing");
```

從邏輯上來看 Swap 與 TSL 並無區別，都是先記錄當前的臨界區是否已上鎖，然後直接上鎖並檢查原本的上鎖狀態

所以Swap與TSL有著一樣的[優缺點](#TSLAD)