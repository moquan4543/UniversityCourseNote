# 互斥鎖

<show-structure/>

解決臨界資源互斥的最簡單工具

## 方法
acquire() 用來獲得鎖 

release() 用來釋放鎖

## 概念
一個進程進入臨界區前應先獲得鎖，退出臨界區時釋放鎖

當調用 acquire() 時會檢查所是否可用，若鎖可用則調用成功並將鎖的狀態改為不可用

<tip>一個 mutex lock 可以理解為一個 bool 型變數，只有兩個狀態</tip>

<code-block lang="D2">
    vars: {
        d2-config: {
            theme-id: 200
        }
    }
    direction: right
    question: acquire()
    question.shape: oval
    yes: 將鎖改為不可用
    no: 阻塞直到鎖可用
    question -> yes: 鎖可用
    question -> no: 鎖不可用
</code-block>

互斥鎖的主要缺點是**忙等待**，若是在單處理機系統上，忙等待的過程不可能解鎖

但是在**多處理機系統**上，如果鎖不可用的時間較短，則等待的**代價會很低**，不需要在等待期間切換上下文，通常一個核忙等，其他核照常工作

<tip>

需要循環忙等待的互斥鎖都可稱為**自旋鎖(spin lock)**
，如 [](ExclusiveHardware.md#testandset) 、 [](ExclusiveHardware.md#swap) 、 [](ExclusiveSoftware.md#singleFlag)
</tip>