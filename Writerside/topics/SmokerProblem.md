# 吸菸者問題

<show-structure/>

## 問題描述
一個系統有**三個抽菸者**進程和**一個供應者**進程，抽菸者會不段捲菸並抽掉，但是捲菸需要三種材料: 菸草、紙、膠水。

三個抽菸者中，**第一個有菸草，第二個有紙、第三個有膠水**。

供應者**每次會放兩種材料到桌子上**，擁有剩下那種材料的抽菸者會捲菸並抽掉它，並給供應者一個完成信號，供應者就會繼續放材料到桌上(會讓三個抽菸者輪流抽菸)

## 問題分析
### 互斥關係
桌子可以抽象為容量為1的緩衝區
兩種材料應該看成一種組合
- 組合一: 紙+膠水
- 組合二: 菸草+膠水
- 組合三: 菸草+紙

### 同步關係
- 桌上有組合一 -> 抽菸者一取走
- 桌上有組合二 -> 抽菸者二取走
- 桌上有組合三 -> 抽菸者三取走
- 發出完成信號 -> 供應者放下一個組合

<code-block lang="d2">
vars: {
    d2-config: {
        theme-id: 200
    }
}
direction: right
provider --> smoker1: V-offer1-P
provider --> smoker2: V-offer2-P
provider --> smoker3: V-offer3-P
smoker1 --> provider: P-finish-V
smoker2 --> provider: P-finish-V
smoker3 --> provider: P-finish-V
</code-block>

<tabs>
<tab title="Semaphore">
    <code-block lang="C++">
        semaphore offer1 = 0;
        semaphore offer2 = 0;
        semaphore offer3 = 0;
        semaphore finish = 0;
        int i = 0;
    </code-block>
</tab>
<tab title="Provider">
    <code-block lang="C++">
        void provider() {
            while(1){
                if(i == 0) {
                    push();
                    V(offer1);
                } else if(i == 1) {
                    push();
                    V(offer2);
                } else if(i == 2) {
                    push();
                    V(offer3);
                }
                i = (i + 1) % 3;
                P(finish);
            }
        }
    </code-block>
</tab>
<tab title="smoker1">
    <code-block lang="C++">
        void smoker1() {
            while(1){
                P(offer1);
                pop();
                V(finish);
            }
        }
    </code-block>
</tab>
<tab title="smoker2">
    <code-block lang="C++">
        void smoker2() {
            while(1){
                P(offer2);
                pop();
                V(finish);
            }
        }
    </code-block>
</tab>
<tab title="smoker3">
    <code-block lang="C++">
        void smoker3() {
            while(1){
                P(offer3);
                pop();
                V(finish);
            }
        }
    </code-block>
</tab>
</tabs>

