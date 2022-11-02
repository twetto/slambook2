# 第六講：非線性最佳化

## 狀態估計問題

### 批量狀態估計與最大後驗估計

回顧第二講的運動方程與觀測方程：

$$
\begin{cases}
x_k = f(x_{k-1}, u_k) + w_k \\
z_{k,j} = h(y_j, x_k) + v_{k,j}
\end{cases}
$$

在討論過相機位姿的表示方法(第三/四講)還有針孔相機模型(第五講)後，我們可以來對這個方程具體地參數化一下：位姿$\boldsymbol{x}_k$可以由$\boldsymbol{T}_k \in SE(3)$表達。觀測方程由針孔模型給定：

$$
s\boldsymbol{z}_{k,j} = \boldsymbol{K}(\boldsymbol{R}_k \boldsymbol{y}_j + \boldsymbol{t}_k)
$$

z:像素位置，y:路標點，x:相機位姿。另外還有雜訊，為了簡化我們先設成常態分佈。

處理這種狀態估計的問題大概分成**增量**(incremental)跟**批量**(batch)兩種方法，看是要每次數據更新就處理一次還是一次處理很多筆數據。增量的經典作法就是卡爾曼濾波，批量的部份，為了追求實時性又不會太不準，SLAM上面經常是折衷一點，僅拿現在附近的一段軌跡進行最佳化(滑動窗口最佳化)。本講我們先學習批量方法，因為學起來比較簡單。

已知控制輸入$\boldsymbol{u}$和觀測數據$\boldsymbol{z}$的條件下求狀態$\boldsymbol{x}, \boldsymbol{y}$的條件概率分布：

$$
P(\boldsymbol{x}, \boldsymbol{y} | \boldsymbol{z}, \boldsymbol{u})
$$

當然大部分的時候不拿(拿不到)控制訊號，只有觀測方程，我們就把u給拿掉：$P(\boldsymbol{x}, \boldsymbol{y} | \boldsymbol{z})$。

利用貝氏定理估計x, y的分佈：

$$
P(\boldsymbol{x}, \boldsymbol{y} | \boldsymbol{z}, \boldsymbol{u})
=
\frac{P(\boldsymbol{z}, \boldsymbol{u} | \boldsymbol{x}, \boldsymbol{y})P(\boldsymbol{x}, \boldsymbol{y})}{P(\boldsymbol{z}, \boldsymbol{u})}
\propto P(\boldsymbol{z}, \boldsymbol{u} | \boldsymbol{x}, \boldsymbol{y})P(\boldsymbol{x}, \boldsymbol{y})
$$

雖然貝氏定理高中就教過了，以防萬一忘記它主要就是說A, B兩種事件中，AB交集機率=A機率\*已知A下AB交集機率=B機率\*已知B下AB交集機率，然後等式兩邊同除啥的。

等式左邊稱為**後驗概率**，右側的$P(\boldsymbol{z}, \boldsymbol{u} | \boldsymbol{x}, \boldsymbol{y})$為**似然**(likelihood)，$P(\boldsymbol{x}, \boldsymbol{y})$稱為**先驗**(Prior)。**直接求後驗分佈是困難的，但求一個狀態最優估計，使得在該狀態下後驗概率最大化**(Maximum-a-posterioli, MAP)(直接照抄課本)，則是可行的：

$$
(\boldsymbol{x}, \boldsymbol{y})^*_{MAP} = \arg\max P(\boldsymbol{x}, \boldsymbol{y} | \boldsymbol{z}, \boldsymbol{u}) = \arg\max P(\boldsymbol{z}, \boldsymbol{u} | \boldsymbol{x}, \boldsymbol{y})P(\boldsymbol{x}, \boldsymbol{y})
$$

意思就是說，我們可以利用最大化「當前位置/路標下的觀測分佈(1)」 X 「當前位置/路標分佈(2)」來等效於「當前觀測下的位置/路標分佈(3)」。(1)可以由觀測取得，(2)則是由之前的狀態來的(先驗)。

當然如果你不知道先驗分佈，也可以改求**最大似然估計**(Maximum likelihood estimation, MLE)：

$$
(\boldsymbol{x}, \boldsymbol{y})^*_{MLE} \arg\max P(\boldsymbol{z}, \boldsymbol{u} | \boldsymbol{x}, \boldsymbol{y})
$$

相當於把上面的(2)給省略掉了；更白話一點說，就是「**在什麼樣的狀態(姿態, 路標位置, etc.)下，最可能產生現在觀測到的數據(角點在圖片空間的位置, IMU讀數, etc.)**」(抄課本真方便)。

### 最小二乘的引出

這段就不寫數學式子了(好麻煩)，嘴巴講講就好。

To be continued...

### 範例

## 非線性最小二乘

### 一階/二階梯度法

### 高斯牛頓法

### Levenburg-Marquadt 法

## 範例：曲線擬合問題

### 手寫高斯牛頓法

### 使用 Ceres

### 使用 g2o (General Graphic Optimization)

## 習題

**證明線性方程$\boldsymbol{Ax}=\boldsymbol{b}$當係數矩陣$\boldsymbol{A}$超定時，最小二乘解為$\boldsymbol{x} = (\boldsymbol{A}^T\boldsymbol{A})^{-1}\boldsymbol{A}^T\boldsymbol{b}。$**

**調研最速下降法、牛頓法、高斯牛頓法和LM方法各有什麼優缺點？除了我們舉的Ceres和g2o庫，還有哪些常用的最佳化函式庫？(你可能會找到一些MATLAB上的庫)**

**為什麼高斯牛頓法的增量方程係數矩陣可能不正定？不正定有什麼幾何涵義？為什麼在這種情況下解就不穩定了？**

**DogLeg是什麼？它與高斯牛頓法和LM法有何異同？請搜索相關的材料。**

**閱讀Ceres的教學材料以更好地掌握其用法。**

**閱讀g2o自帶的文檔，你能看懂它嗎？如果還不能完全看懂，請在閱讀第10/11講後再回來看。**

**請更改曲線擬合實驗中的曲線模型，並用Ceres和g2o進行最佳化實驗。例如可以使用更多的參數和更複雜的模型。**

