# 第十二講：建圖

主要目標：

1. 理解單目SLAM中稠密深度估計的原理

2. 通過實驗了解單目稠密重建的過程

3. 了解幾種RGB-D重建中的地圖形式。

## 概述

地圖的用處：

1. 定位: 稀疏地圖即可做到

2. 導航: 知道道路跟障礙物在哪裡，並規劃路線。

3. 避障: 跟導航一樣，只是更注重局部、動態障礙物的處理。

4. 重建: 給人看的，需要附上灰度或RGB訊息。想像玩遊戲時的畫面呈現。

5. 互動: 不管是人透過AR/VR或機器跟人/環境互動，都會需要更高級的物理/語義地圖。

除了第一點之外，2. 3. 4. 主要是稠密，5. 則需要更高階的理解。

## 單目稠密重建

### 立體視覺

用原本只能測角度的相機測深度:

1. 單目: SfM

2. 雙目: 視差法

3. RGB-D: 不管啦，用主動式的紅外光/聲納/光達直接獲得深度

### 極線搜索與塊匹配(block matching)

### 常態分佈的深度濾波

## 實踐：單目稠密重建

### 實驗分析與討論

### 像素梯度的問題

### 逆深度

### 圖像間的變換

### 並行化：效率的問題

### 其他可能的改進

## RGB-D稠密建圖

### 實踐：點雲地圖

### 從點雲重建網格

### 八叉樹地圖

### 實踐：八叉樹地圖

## TSDF地圖和Fusion系列

## 習題

**推導式(12.6):**

$$
\mu_{fuse} = \frac{\sigma^2_{obs}\mu + \sigma^2\mu_{obs}}{\sigma^2 + \sigma^2_{obs}},\: 
\sigma^2_{fuse} = \frac{\sigma^2\sigma^2_{obs}}{\sigma^2 + \sigma^2_{obs}}
$$

**把本講的稠密深度估計成半稠密，你可以先把梯度明顯的地方篩選出來。**

**把本講演示的單目稠密重建代碼從正深度改成逆深度，並添加仿射變換。實驗效果是否有改進？**

**你能論證如何在八叉樹中進行導航或路徑規劃嗎？**

**研究參考文獻，探討TSDF地圖是如何進行為資估計和更新的，它和我們之前講過的定位建圖算法有何異同？**

**研究均勻-高斯混合濾波器的原理與實現。**