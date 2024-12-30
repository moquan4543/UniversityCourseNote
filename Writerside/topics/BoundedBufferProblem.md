# 生產者-消費者問題

<show-structure/>

## 問題描述
- 系統中有一組生產者進程和一組消費者進程
- 生產者每次生產一個產品放入緩衝區
- 消費者每次從緩衝區取出一個產品
- 生產者、消費者共享一個初始為空，大小為 n 的緩衝區
- 只有緩衝區**沒滿時，生產者才能生產**，否則須等待
- 只有緩衝區**不空時，消費者才能取出**，否則須等待
- 緩衝區是**臨界資源**

## 問題分析
1. **關係分析**。找出同步、互斥關係
2. **整理思路**。確定P、V操作大致順序
3. **設置信號量**。互斥信號量初值一般為1，同步信號量看資源的初始值

<code-block lang="d2">
vars: {
    d2-config: {
        theme-id: 200
    }
}
direction: right

緩衝區非空 ---> 消費者消費
緩衝區沒滿 ---> 生產者生產
</code-block>

問題需要三個信號量
<code-block lang="c++">
semaphore mutex = 1;    //互斥信號量，實現緩衝區互斥訪問
semaphore empty = n;    //同步信號量，表示緩衝區空閒數
semaphore full = 0;     //同步信號量，表示產品數
</code-block>

所以最終的進程是
<tabs>
<tab title="Producer">
<code-block lang="C++">
void producer() {
    while(1) {
        P(empty);
        P(mutex);
        push();
        V(mutex);
        V(full);
    }
}
</code-block>

</tab>
<tab title="Consumer">
<code-block lang="C++">
void consumer() {
    while(1) {
        P(full);
        P(mutex);
        pop();
        V(mutex);
        V(empty);
    }
}
</code-block>
</tab>
</tabs>