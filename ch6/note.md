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

在討論過相機位姿的表示方法(第三/四講)還有針孔相機模型(第五講)後，我們可以來對這個方程具體地參數化一下：位姿 $\boldsymbol{x}_k$ 可以由 $\boldsymbol{T}_k \in SE(3)$ 表達。觀測方程由針孔模型給定：

$$
s\boldsymbol{z}_{k,j} = \boldsymbol{K}(\boldsymbol{R}_k \boldsymbol{y}_j + \boldsymbol{t}_k)
$$

z:像素位置，y:路標點，x:相機位姿。另外還有雜訊，為了簡化我們先設成常態分佈。

處理這種狀態估計的問題大概分成**增量**(incremental)跟**批量**(batch)兩種方法，看是要每次數據更新就處理一次還是一次處理很多筆數據。增量的經典作法就是卡爾曼濾波，批量的部份，為了追求實時性又不會太不準，SLAM上面經常是折衷一點，僅拿現在附近的一段軌跡進行最佳化(滑動窗口最佳化)。本講我們先學習批量方法，因為學起來比較簡單。

已知控制輸入 $\boldsymbol{u}$ 和觀測數據 $\boldsymbol{z}$ 的條件下求狀態 $\boldsymbol{x}, \boldsymbol{y}$ 的條件概率分布：

$$
P(\boldsymbol{x}, \boldsymbol{y} | \boldsymbol{z}, \boldsymbol{u})
$$

當然大部分的時候不拿(拿不到)控制訊號，只有觀測方程，我們就把u給拿掉： $P(\boldsymbol{x}, \boldsymbol{y} | \boldsymbol{z})$ 。

利用貝氏定理估計x, y的分佈：

$$
P(\boldsymbol{x}, \boldsymbol{y} | \boldsymbol{z}, \boldsymbol{u}) = \frac{P(\boldsymbol{z}, \boldsymbol{u} | \boldsymbol{x}, \boldsymbol{y})P(\boldsymbol{x}, \boldsymbol{y})}{P(\boldsymbol{z}, \boldsymbol{u})}
\propto P(\boldsymbol{z}, \boldsymbol{u} | \boldsymbol{x}, \boldsymbol{y})P(\boldsymbol{x}, \boldsymbol{y})
$$

雖然貝氏定理高中就教過了，以防萬一忘記它主要就是說A, B兩種事件中，AB交集機率=A機率\*已知A下AB交集機率=B機率\*已知B下AB交集機率，然後等式兩邊同除啥的。

等式左邊稱為**後驗概率**，右側的 $P(\boldsymbol{z}, \boldsymbol{u} | \boldsymbol{x}, \boldsymbol{y})$ 為**似然**(likelihood)， $P(\boldsymbol{x}, \boldsymbol{y})$ 稱為**先驗**(Prior)。**直接求後驗分佈是困難的，但求一個狀態最優估計，使得在該狀態下後驗概率最大化**(Maximum-a-posterioli, MAP)(直接照抄課本)，則是可行的：

$$
(\boldsymbol{x}, \boldsymbol{y})^*_{MAP} = \arg\max P(\boldsymbol{x}, \boldsymbol{y} | \boldsymbol{z}, \boldsymbol{u}) = \arg\max P(\boldsymbol{z}, \boldsymbol{u} | \boldsymbol{x}, \boldsymbol{y})P(\boldsymbol{x}, \boldsymbol{y})
$$

意思就是說，我們可以利用最大化「當前位置/路標下的觀測分佈(1)」 X 「當前位置/路標分佈(2)」來等效於「當前觀測下的位置/路標分佈(3)」。(1)可以由觀測取得，(2)則是由之前的狀態來的(先驗)。

當然如果你不知道先驗分佈，也可以改求**最大似然估計**(Maximum likelihood estimation, MLE)：

$$
(\boldsymbol{x}, \boldsymbol{y})^*_{MLE} = \arg\max P(\boldsymbol{z}, \boldsymbol{u} | \boldsymbol{x}, \boldsymbol{y})
$$

相當於把上面的(2)給省略掉了；更白話一點說，就是「**在什麼樣的狀態(姿態, 路標位置, etc.)下，最可能產生現在觀測到的數據(角點在圖片空間的位置, IMU讀數, etc.)**」(抄課本真方便)。

### 最小二乘的引出

這段就不寫數學式子了(好麻煩)，嘴巴講講就好。

假設觀測模型雜訊遵守常態分佈，那我們就求分佈的峰值所在位置就行了。不過常態分佈的PDF裡面有個指數很難算，所以我們改求常態分佈的負對數，把指數拿掉；因為對數是單調遞增的，求常態分佈的峰值位置就等於求它負對數的谷值(最小)位置。這就變成了最小化由共變異數矩陣的反矩陣加權的、「實際觀測」與「待估計狀態經過觀測模型後的理論觀測值」之間的歐式距離。這個距離又叫馬哈拉諾比斯距離(Mahalanobis distance, 簡稱馬氏距離)。

假設在一段時間中的批量數據，每個輸入跟觀測都是互相獨立的，我們就可以單純地把各項誤差加起來(對數讓聯合分佈的指數相乘變相加)，變成一個**最小二乘問題**。

### 範例

拿一輛沿x軸前進後退的車子做舉例，共四個時間步的批量數據。略。

## 非線性最小二乘

考慮一個最小二乘問題：

$$
\min_x F(\boldsymbol{x}) = \frac{1}{2}\|f(\boldsymbol{x})\|^2_2
$$

求目標函數 $F$ 最小值，比較簡單的是找梯度為零的地方，不過有的時候梯度函數很複雜，我們就改用迭代的方式，一次只走一小步，直到找到最小值。下面介紹幾個迭代方法。

### 一階/二階梯度法

要找增量最直觀的就是對目標函數泰勒展開，拿導數一階雅可比跟二階海塞矩陣。如果只取一階就是最速下降法，取一/二階就是牛頓法。具體的矩陣求導請參考附錄。有時候一階太貪心會走出鋸齒狀路線，反而增加收斂步數，而二階海塞有點難算，所以以下介紹其他方法。

### 高斯牛頓法

與其直接求目標函數 $F$ 最小值，不如改對 $f(\boldsymbol{x})$ 進行一階泰勒展開：

$$
f(\boldsymbol{x} + \Delta \boldsymbol{x}) \approx f(\boldsymbol{x}) + \boldsymbol{J}(\boldsymbol{x})^T \Delta \boldsymbol{x}
$$

並改求：

$$
\Delta\boldsymbol{x}^* = \arg\min_{\Delta\boldsymbol{x}} \frac{1}{2} \|f(\boldsymbol{x}) + \boldsymbol{J}(\boldsymbol{x})^T\Delta\boldsymbol{x}\|^2
$$

展開：

$$
\begin{align}
\frac{1}{2} \|f(\boldsymbol{x}) + \boldsymbol{J}(\boldsymbol{x})^T\Delta\boldsymbol{x}\|^2 &= \frac{1}{2} \Bigl(f(\boldsymbol{x}) + \boldsymbol{J}(\boldsymbol{x})^T\Delta\boldsymbol{x}\Bigr)^T \Bigl(f(\boldsymbol{x}) + \boldsymbol{J}(\boldsymbol{x})^T\Delta\boldsymbol{x}\Bigr) \\
&= \frac{1}{2} \Bigl( \|f(\boldsymbol{x})\|^2_2 + 2f(\boldsymbol{x})\boldsymbol{J}(\boldsymbol{x})^T \Delta\boldsymbol{x} + \Delta \boldsymbol{x}^T\boldsymbol{J}(\boldsymbol{x})\boldsymbol{J}(\boldsymbol{x})^T \Delta\boldsymbol{x} \Bigr)
\end{align}
$$

再求對增量 $\Delta\boldsymbol{x}$ 的導數(偏微)，令其為零：

$$
\boldsymbol{J}(\boldsymbol{x})f(\boldsymbol{x}) + \boldsymbol{J}(\boldsymbol{x})\boldsymbol{J}^T(\boldsymbol{x})\Delta\boldsymbol{x} = 0
$$

$$
\boldsymbol{J}(\boldsymbol{x})\boldsymbol{J}^T(\boldsymbol{x})\Delta\boldsymbol{x} = -\boldsymbol{J}(\boldsymbol{x})f(\boldsymbol{x})
$$

把左邊係數定義為H，右邊定義為g，我們把它記成：

$$
\boldsymbol{H}\Delta\boldsymbol{x} = \boldsymbol{g}
$$

這個方程被叫作增量方程、高斯牛頓方程(G-N equation)、正規方程(normal equation)。我們用 $JJ^T$ 作為牛頓法裡面二階海塞的近似，這樣就不用計算「真正的」 $H$ 了。

當然這種近似有可能會出問題，像是 $JJ^T$ 奇異或病態時 $\Delta x$ 會不穩甚至無法收斂，或者求出的 $\Delta x$ 有時會太大到超出了近似的有效範圍之類的。

### Levenburg-Marquardt 法

我們在高斯牛頓中給 $\Delta \boldsymbol{x}$ 添加一個叫作**信賴區域**的範圍來限制它不要跑到近似不準的地方，定義在什麼情況下二階近似是有效的。我們拿一個指標來確認有效程度：

$$
\rho = \frac{f(\boldsymbol{x} + \Delta \boldsymbol{x} ) - f( \boldsymbol{x})}{\boldsymbol{J}(\boldsymbol{x})^T \Delta \boldsymbol{x}}
$$

上式分子是實際函數下降的值，分母是近似模型預估下降的值。越接近1表示估計越準，如果太小就縮小近似範圍，如果很準就放大範圍：

$$
\Delta\boldsymbol{x}^* = \arg\min_{\Delta\boldsymbol{x}} \frac{1}{2} \|f(\boldsymbol{x}) + \boldsymbol{J}(\boldsymbol{x})^T\Delta\boldsymbol{x}\|^2, \; s.t. \|\boldsymbol{D} \Delta \boldsymbol{x} \|^2 \leq \mu \\
$$
Calculate $\rho$ .
$$
\begin{cases}
if\; \rho > \frac{3}{4},\; then\; \mu = 2\mu \\
if\; \rho < \frac{1}{4},\; then\; \mu = 0.5\mu
\end{cases}
$$

如此就等於是把增量限制在半徑為 $\mu$ 的球中。如果係數矩陣 $\boldsymbol{D}$ 是單位矩陣就是一個正球形(Levenberg)，如果非負對角矩陣就是橢球(Marquardt)；實務上常用 $\boldsymbol{J}^T\boldsymbol{J}$ 的對角元素平方根，等於是讓橢球梯度大的方向比較長，梯度小的方向比較短。

我們用拉格朗日乘數 $\lambda$ 將這個球形限制放到目標函數：

$$
\mathcal{L}(\Delta\boldsymbol{x},\; \lambda) = \frac{1}{2} \Bigl\|f(\boldsymbol{x}) + \boldsymbol{J}(\boldsymbol{x})^T\Delta\boldsymbol{x}) \Bigr\|^2 + \frac{\lambda}{2}\Bigl( \|\boldsymbol{D}\Delta\boldsymbol{x}\|^2 - \mu \Bigr)
$$

一樣對 $\Delta\boldsymbol{x}$ 偏微，另其為零：

$$
(\boldsymbol{H}+\lambda\boldsymbol{D}^T\boldsymbol{D}) \Delta\boldsymbol{x} = \boldsymbol{g}
$$

可以看到這裡比高斯牛頓多了一項 $\lambda\boldsymbol{D}^T\boldsymbol{D}$ 。當 $\lambda$ 比較小時，表示這個問題不錯，可以用高斯牛頓法；比較大時，表示二次近似不好，改用(類)一階最速下降法，步長由 $\mu$ 來調整。

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

