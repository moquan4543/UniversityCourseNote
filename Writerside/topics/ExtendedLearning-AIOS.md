# 【17周彈性】AIOS

<show-structure/>
<chapter title="與時俱進的 OS (Background)" collapsible="true" default-state="expanded">

### Windows
Microsoft Windows在發展的時候，**Compatible** 的概念非常重要，採取的是通用的硬體、通用的作業系統，把兩者分離。

Windows 有良好的架構設計，對 Intel x86 系列有很好的支持，壟斷 **PC** 市場

### Linux
技術上最大的特點是在 Kernel 層面支持 TCP/IP 協議，非常契合**網路時代**

### Android
手機快速發展，滿足的是能夠用**非常低的成本快速交付智慧型手機**

### 下一個?
如果 AI 並沒有出現，猜應該會是往**物聯網**的方向發展

人口的增長已經不會再有大變動，但是萬物連網的時代下**聯網設備數量一定會越來越多**

但是此時的局面是 **AI 問世並大行其道**，那麼作業系統發展的方向勢必會受到影響

</chapter>


## AI 操作系統
<compare first-title="傳統作業系統" second-title="AI 作業系統">
<code-block lang="markdown">
- 以硬體為導向
</code-block>

<code-block lang="markdown">
- 以資料為導向
</code-block>
</compare>

**傳統的作業系統**算是一個硬體與軟體的中間層，協助軟體去驅動硬體，**管理好硬體資源**是非常重要的

但是 **AI 作業系統**更多的是專注在**資料的處理**，由於人工智慧整個計算過程大概可以擬為大量的資料輸入、輸出

所以相對於傳統的作業系統，需要設計更多**管理資料的 module、功能、邏輯等**

<compare first-title="傳統作業系統" second-title="AI 作業系統">
<code-block lang="markdown">
- 明確的指令互動
</code-block>

<code-block lang="markdown">
- 自然語言互動
</code-block>
</compare>

AI 為人機互動帶來的變革非常巨大，以往都是**透過 IO 設備與機器進行互動**，比如鍵盤、滑鼠對應固定的鍵值

但是 AI 發展到現在，人類與機器的互動方式開始有模糊化的趨勢，可以由**自然語言直接與機器交互**

<compare first-title="傳統作業系統" second-title="AI 作業系統">
<code-block lang="markdown">
- 管理 Process/Threads
</code-block>

<code-block lang="markdown">
- 管理模型
</code-block>
</compare>

這個學期上完作業系統，其實大量的內容都是聚焦在**管理 Process、管理 Threads** 上，例如占了很大篇幅的調度算法。

而對 AI 為主的作業系統來說，這並不是需要非常著重的點，而是需要研究**模型的管理**，模型的推理訓練、增強、壓縮等等一系列的過程
此時作業系統的角色更像是一個管理者，管理 AI 的 Instance

<compare first-title="傳統作業系統" second-title="AI 作業系統">
<code-block lang="markdown">
- 人工開發
</code-block>

<code-block lang="markdown">
- 人工 + 訓練 + 生成
</code-block>
</compare>

在作業系統開發的過程也有一定的不同，傳統的作業系統都是由一大群**人去開發**的

但對於 AIOS 來說，可能就會包含很多由 **AI 生成**的東西
如此能夠做到的是一些視窗文字、提示訊息等等就不會再是固定的東西

## 例子

### What is running

一個簡單的例子是可以透過問問題的方式去查看目前整個機器的運作情況
若是傳統作業系統，會需要去查看手冊，可能還需要下多個指令才能掌握到情況

但是對於 AIOS 來說，只需要**將需求以自然語言輸入**，就能夠根據需求顯示想查看的 status、log 等資訊

### Setup environment

想像有一台 Server 當作生產環境，當我想要進入環境進行開發時需要先連上這台機器

步驟會是:
<procedure>
<step>
連接網路
</step>
<step>
連接VPN
</step>
<step>
啟動遠端桌面
</step>
<step>
打開 IDE
</step>
</procedure>

這幾個步驟涉及到**不同介面的操作**，但理想情況下，對於 AIOS 來說只需要**下 Prompt 表達自己想要連到 Server 並準備開始開發** 後面甚至都不需要使用者手動操作，能夠極大提升速度
