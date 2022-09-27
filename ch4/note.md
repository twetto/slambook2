# 第四講: 李群與李代數

可怕的一講。大量的數學。兔ㄌ。

上一講我們學習了怎麼表示旋轉。這講我們討論李代數。它們之間是什麼關係？

除了表示，我們還要對它們進行估計和最佳化。旋轉矩陣最佳化時需引入額外的約束(正交且行列式為1)，而這會讓最佳化的過程顧這顧那的很麻煩。我們希望透過轉換成李代數，把約束拿掉，簡單求解。

## 基礎

### 群的定義

集合記作 $A$ ，運算記作 $\cdot$ ，群記作 $G=(A,\cdot)$ ，要求以下條件：

1. 封閉性: $\forall a_1, a_2 \in A,\: a_1 \cdot a_2 \in A$

2. 結合性: $\forall a_1, a_2, a_3 \in A,\: (a_1 \cdot a_2) \cdot a_3 = a_a \cdot (a_2 \cdot a_3)$

3. 單位元素(么元): $\exists a_0 \in A,\: s.t.\: \forall a \in A,\: a_0 \cdot a = a \cdot a_0 = a$

4. 反元素(逆): $\forall a \in A,\: \exists a^{-1} \in A,\: s.t.\: a \cdot a^{-1} = a_0$

而**李群**則是指具有連續(光滑)性質的群。

### 李代數的引出(略)

主要是給你一點對李代數的感覺；透過對旋轉矩陣對時間的求導來得到李群--李代數的指數/對數映射的概念。

### 李代數的定義

李代數描述了李群單位元附近的正切空間，定義如下：

集合 $\mathbb{V}$ 、數域 $\mathbb{F}$ 、二元運算 $[,]$ ，李代數 $\mathfrak{g}=(\mathbb{V}, \mathbb{F},[,])$ 需具備以下性質：

1. 封閉性: $\forall \textbf{X}, \textbf{Y} \in \mathbb{V},\: [\textbf{X}, \textbf{Y}] \in \mathbb{V}$

2. 雙線性: $\forall \textbf{X}, \textbf{Y}, \textbf{Z} \in \mathbb{V},\: a, b \in \mathbb{F}$, 有

$$
[a\textbf{X}+b\textbf{Y}, \textbf{Z}] = a[\textbf{X}, \textbf{Z}] + b[\textbf{Y}, \textbf{Z}],\: [\textbf{Z}, a\textbf{X}+b\textbf{Y}] = a[\textbf{Z}, \textbf{X}] + b[\textbf{Z}, \textbf{Y}]
$$

3. 自反性: $\forall \textbf{X} \in \mathbb{V}, [\textbf{X},\textbf{X}] = \textbf{0}$

4. 雅可比等價: $\forall \textbf{X}, \textbf{Y}, \textbf{Z} \in \mathbb{V},\: [\textbf{X}, [\textbf{Y}, \textbf{Z}]] + [\textbf{Z}, [\textbf{X}, \textbf{Y}]] + [\textbf{Y}, [\textbf{Z}, \textbf{X}]] = \textbf{0}$

其中，二元運算被稱為**李括號**。比方說外積就是一種李括號。

題外話，我們[選擇單位元當作基準只是因為它在每個李群都有](https://math.stackexchange.com/questions/720469/lie-algebra-why-does-it-have-to-be-the-tangent-space-at-the-identity-of-a-lie-g)。

要特別注意，在接下來的內容中我們默認李代數的數域 $\mathbb{F} \in \mathbb{R}$ ，這樣我們就能專注在李代數集合(更具體一點是向量空間)跟李括號上面。

### 李代數 $\mathfrak{so}(3)$

SO(3)對應的李代數 $\mathfrak{so}(3)$ (元素?)是定義在 $\mathbb{R}^3$ 上的向量，記作 $\phi$ 。每個 $\phi$ 都可以生成一個反對稱矩陣：

$$
\boldsymbol{\Phi} = \phi^\wedge =
\begin{bmatrix}
0 & -\phi_3 & \phi_2 \\
\phi_3 & 0 & -\phi_1 \\
-\phi_2 & \phi_1 & 0
\end{bmatrix} \in \mathbb{R}^{3\times3}
$$

由此我們定義出 $\mathfrak{so}(3)$ 李代數集合的元素是三維向量或三維反對稱矩陣:

$$
\mathfrak{so}(3) = \{\phi \in \mathbb{R}^3, \boldsymbol{\Phi} = \phi^\wedge \in \mathbb{R}^{3\times3}\}
$$

因為每個 $\phi$ 跟 $\Phi$ 都是一一對應的，我們就把它寫在一起不做區別。

$\mathfrak{so}(3)$ 的李括號：

$$
[\phi_1, \phi_2] = (\boldsymbol{\Phi}_1 \boldsymbol{\Phi}_2 - \boldsymbol{\Phi}_2 \boldsymbol{\Phi}_1)^\vee
$$

### 李代數 $\mathfrak{se}(3)$

$$
\mathfrak{se}(3) = \{\boldsymbol{\xi} = 
\begin{bmatrix}
\boldsymbol{\rho} \\
\phi
\end{bmatrix} \in \mathbb{R}^6,\: 
\boldsymbol{\rho} \in \mathbb{R}^3,\:
\phi \in \mathfrak{so}(3),\:
\boldsymbol{\xi}^\wedge = 
\begin{bmatrix}
\phi^\wedge & \boldsymbol{\rho} \\
\textbf{0}^T & 0
\end{bmatrix} \in \mathbb{R}^{4 \times 4}
\}
$$

要注意 $\boldsymbol{\xi}^\wedge$ 不再只是表達反對稱矩陣，但仍表示矩陣<-->向量的關係。

$\mathfrak{se}(3)$ 的李括號如下：

$$
[\boldsymbol{\xi}_1, \boldsymbol{\xi}_2] = (\boldsymbol{\xi}_1^\wedge \boldsymbol{\xi}_2^\wedge - \boldsymbol{\xi}_2^\wedge \boldsymbol{\xi}_1^\wedge)^\vee
$$

## 指數與對數映射

### SO(3)上的指數映射

簡單的介紹 $\phi \in \mathfrak{so}(3)$ 如何進行指數映射。先利用泰勒展開表達，計算它的無窮次冪時發現pattern是重複的(詳細請參考紙本書, 好懶喔)。總之最後我們得到這個式子：

定 $\phi$ 的模長和方向(單位向量)為 $\theta, \textbf{a}$ :

$$
\exp(\phi) = \exp(\theta\textbf{a}^\wedge) = \cos \theta \textbf{I} + (1 - \cos \theta) \textbf{a}\textbf{a}^T + \sin \theta \textbf{a}^\wedge
$$

**震驚！** $\mathfrak{so}(3)$ **的指數映射竟然就是羅德里格斯公式！**(想不到吧)

SO(3)的對數映射：

$$
\phi = \ln (\textbf{R})^\vee = (\sum^{\infty}_{n=0} \frac{(-1)^n}{n+1} (\textbf{R} - \textbf{I})^{n+1})^\vee
$$

不過這個式子不用硬用泰勒展開解，只要比照第三講用矩陣跡把轉角跟轉軸拿出來就行了。

要特別注意把旋轉角度限制在 $\pm \pi$ 之間，不然SO(3)跟 $\mathfrak{so}(3)$ 不是一一對應的。

### SE(3)上的指數映射

$$
\exp(\boldsymbol{\xi}^\wedge) = 
\begin{bmatrix}
\sum_{n=0}^{\infty} \frac{1}{n!} (\phi^\wedge)^n & \sum_{n=0}^{\infty} \frac{1}{(n+1)!} (\phi^\wedge)^n \boldsymbol{\rho} \\
\textbf{0}^T & 1
\end{bmatrix} \triangleq
\begin{bmatrix}
\textbf{R} & \textbf{J}\boldsymbol{\rho} \\
\textbf{0}^T & 1
\end{bmatrix} = \textbf{T}
$$

其中 $\textbf{J}$ 的推導跟推羅德里格斯公式時一樣進行泰勒展開後發現重複pattern並整理成下式：

$$
\textbf{J} = \frac{\sin \theta}{\theta} \textbf{I} + (1 - \frac{\sin \theta}{\theta}) \textbf{a}\textbf{a}^T + \frac{1 - \cos \theta}{\theta}\textbf{a}^\wedge
$$

SE(3)的對數映射可以照上述方法反推，不過更簡單的是先求 $\textbf{R}$ 的旋轉向量再用上式跟 $\textbf{t} = \textbf{J}\boldsymbol{\rho}$ 求得 $\textbf{J}$ 跟 $\boldsymbol{\rho}$ 。

總之，我們在此順過一遍李群<-->李代數的對應關係，並整理成紙本書圖4-1，這裡就不貼了。

## 李代數求導與擾動模型

### BCH公式與其近似

Baker-Campbell-Hausdorff公式描述了兩個李代數指數映射乘積的完整形式。在純量的世界， $\ln(\exp(a)\exp(b)) = a + b$ 是很正常的事，但在矩陣時不成立，而是以下形式：

$$
\ln(\exp(\textbf{A})\exp(\textbf{B})) = \textbf{A} + \textbf{B} + \frac{1}{2} [\textbf{A}, \textbf{B}] + \frac{1}{12} [\textbf{A}, [\textbf{A}, \textbf{B}]] - \frac{1}{12} [\textbf{B}, [\textbf{A}, \textbf{B}]] + \dots
$$

特別地考慮SO(3)上的李代數 $\ln(\exp(\phi_1^\wedge)\exp(\phi_2^\wedge))$ ，當 $\phi_1^\wedge$ 或 $\phi_2^\wedge$ 為小量時，小量二次以上的項可以被忽略。此時BCH擁有線性近似表達：

$$
\ln(\exp(\phi_1^\wedge)\exp(\phi_2^\wedge))^\vee \approx
\begin{cases}
\textbf{J}_l(\phi_2)^{-1} \phi_1 + \phi_2,\: 當\phi_1為小量,\\
\textbf{J}_r(\phi_1)^{-1} \phi_2 + \phi_1,\: 當\phi_2為小量.
\end{cases}
$$

以第一個近似為例，對一個旋轉矩陣 $\textbf{r}_2$ 左乘微小之 $\textbf{r}_1$ 時，可以近似看作原有的李代數 $\phi_2$ 上加上了一項 $\textbf{J}_l(\phi_2)^{-1} \phi_1$ 。第二個近似就是右乘的版本。使用上需要注意是用哪一個版本。(一般來說原碼註釋或原始論文會推導，再看看)

左乘的 $\textbf{J}_l$ 就是剛剛在 SE(3) 推導的 $\textbf{J}$ 。它的逆為：

$$
\textbf{J}_l^{-1} = \frac{\theta}{2} \cot \frac{\theta}{2} \textbf{I} + (1 - \frac{\theta}{2} \cot \frac{\theta}{2}) \textbf{a}\textbf{a}^T - \frac{\theta}{2} \textbf{a}^\wedge
$$

右乘就是對自變量加個負號就好了：

$$
\textbf{J}_r(\phi) = \textbf{J}_l(-\phi)
$$

到這邊為止，我們就能討論李群乘法對應李代數加法的關係了。我們可以利用在李代數上做微積分，來迴避李群算微積分很麻煩的問題。加法萬歲。

### SO(3)上的李代數求導

我們接下來看看求導是怎麼個求法。思路大概分成兩種：

1. 用李代數表示姿態，然後根據李代數加法對其直接求導。

2. 對李群左乘/右乘微小擾動，然後對該擾動求導，稱為左擾動/右擾動模型。

能使用擾動模型的前提是我們獲取觀測數據時的相機姿態是共用的，不然光是要用哪個姿態當作擾動的基準點就都不知道了。(嗎？)

#### 直接求導

我們對一個空間點$\textbf{p}$進行旋轉$\textbf{R}$，得到了$\textbf{Rp}$。我們要計算$\textbf{Rp}$對於$\textbf{R}$的導數，但是SO(3)沒有加法，所以我們轉到李代數上面進行導數計算。設$\textbf{R}$對應的李代數為$\phi$，計算:

$$
\begin{align}
\frac{\partial (\exp(\phi^\wedge)\textbf{p})}{\partial \phi} &= \lim_{\delta \phi \to 0} \frac{\exp((\phi + \delta\phi)^\wedge)\textbf{p} - \exp(\phi^\wedge)\textbf{p}}{\delta \phi} \\
&= \lim_{\delta \phi \to 0} \frac{\exp((\textbf{J}_l \delta\phi)^\wedge)\exp(\phi^\wedge)\textbf{p} - \exp(\phi^\wedge)\textbf{p}}{\delta\phi} \\
&= \lim_{\delta \phi \to 0} \frac{(\textbf{I} + (\textbf{J}_l \delta\phi)^\wedge) \exp(\phi^\wedge)\textbf{p} - \exp(\phi^\wedge)\textbf{p}}{\delta\phi} \\
&= \lim_{\delta \phi \to 0} \frac{(\textbf{J}_l \delta\phi)^\wedge \exp(\phi^\wedge)\textbf{p}}{\delta\phi} \\
&= \lim_{\delta \phi \to 0} \frac{-(\exp(\phi^\wedge)\textbf{p})^\wedge\textbf{J}_l \delta\phi}{\delta\phi} = -(\textbf{Rp})^\wedge\textbf{J}_l
\end{align}
$$

第一$\rightarrow$第二行為PCH線性近似，二$\rightarrow$三為泰勒展開捨去高階項的近似(根據L'Hopital's rule 取極限後可直接等號)，4 $\rightarrow$ 5將反對稱符號看作外積，交換之後變號。不過$\textbf{J}_l$的計算還是比較複雜的，所以就有了下面的擾動模型。

#### 擾動模型(左乘)

對$\textbf{R}$進行一次擾動$\Delta\textbf{R}$，看$\textbf{Rp}$相對於$\Delta\textbf{R}$的變化率。以左擾動為例：

$$
\begin{align}
\frac{\partial(\textbf{Rp})}{\partial\varphi} &= \lim_{\varphi\to 0} \frac{\exp(\varphi^\wedge)\exp(\phi^\wedge)\textbf{p} - \exp(\phi^\wedge)\textbf{p}}{\varphi} \\
&= \lim_{\varphi\to 0} \frac{(\textbf{I}+\varphi^\wedge) \exp(\phi^\wedge) \textbf{p} - \exp(\phi^\wedge)\textbf{p}}{\varphi} \\
&= \lim_{\varphi\to 0} \frac{\varphi^\wedge\textbf{Rp}}{\varphi} = \lim_{\varphi\to 0} \frac{-(\textbf{Rp})^\wedge \varphi}{\varphi} = -(\textbf{Rp})^\wedge
\end{align}
$$

(問：怎麼在實作上區分直接求導/擾動模型？)

### SE(3)上的李代數求導

#### 直接求導(略)

#### 擾動模型(左乘)

設變換$\textbf{T}$的李代數為$\boldsymbol{\xi}$，左乘一擾動$\Delta\textbf{T} = \exp(\delta \boldsymbol{\xi}^\wedge)$；其中設$\delta\boldsymbol{\xi} = [\delta \boldsymbol{\rho}, \delta \phi]^T$:

$$
\begin{align}
\frac{\partial (\textbf{Tp})}{\partial \delta \boldsymbol{\xi}} &= \lim_{\delta\boldsymbol{\xi} \to 0} \frac{\exp(\delta\boldsymbol{\xi}^\wedge)\exp(\boldsymbol{\xi}^\wedge)\textbf{p} - \exp(\boldsymbol{\xi}^\wedge)\textbf{p}}{\delta\boldsymbol{\xi}} \\
&= \lim_{\delta\boldsymbol{\xi} \to 0} \frac{(\textbf{I} + \delta\boldsymbol{\xi}^\wedge)\exp(\boldsymbol{\xi}^\wedge)\textbf{p} - \exp(\boldsymbol{\xi}^\wedge)\textbf{p}}{\delta\boldsymbol{\xi}} \\
&= \lim_{\delta\boldsymbol{\xi} \to 0} \frac{\delta\boldsymbol{\xi}^\wedge \exp(\boldsymbol{\xi}^\wedge)\textbf{p}}{\delta\boldsymbol{\xi}} \\ 
&= \lim_{\delta\boldsymbol{\xi} \to 0}
  \frac{\begin{bmatrix}
    \delta\phi^\wedge & \delta \boldsymbol{\rho} \\
    \textbf{0}^T & 0 \end{bmatrix}\begin{bmatrix}
    \textbf{Rp}+\textbf{t} \\
    1 \end{bmatrix}
  }{\delta\boldsymbol{\xi}} \\
&= \lim_{\delta\boldsymbol{\xi} \to 0}
  \frac{\begin{bmatrix}
    \delta\phi^\wedge(\textbf{Rp}+\textbf{t}) + \delta \boldsymbol{\rho} \\
    \textbf{0}^T \end{bmatrix}
  }{[\delta \boldsymbol{\rho}, \delta \phi]^T} = \left[\begin{array}{cc}
\boldsymbol{I} & -(\boldsymbol{R} \boldsymbol{p}+\boldsymbol{t})^{\wedge} \\
\mathbf{0}^{\mathrm{T}} & \mathbf{0}^{\mathrm{T}}
\end{array}\right] \stackrel{\text { def }}{=}(\boldsymbol{T} \boldsymbol{p})^{\odot}
\end{align}
$$

## 實踐: Sophus

## 相似變換群與其李代數

雖然單目視覺會用到，但我的終極目標並沒有將相似變換群列為必學，所以這邊直接略過。我還是有看喔。沒有偷懶喔。嗯對

## 習題

**驗證SO(3), SE(3), Sim(3)是否符合乘法成群。**

**驗證**$(\mathbb{R}^3, \mathbb{R}, \times)$**構成李代數。**

**驗證**$\mathfrak{so}(3)$**和**$\mathfrak{se}(3)$**滿足李代數要求的性質。**

**驗證性質(4.20)和(4.21)。**

**證明：**

$$
\textbf{Rp}^\wedge \textbf{R}^T = (\textbf{Rp})^\wedge
$$

**證明：**

$$
\textbf{R} \exp(\textbf{p}^\wedge) \textbf{R}^T = \exp((\textbf{Rp})^\wedge)
$$

**該式稱為SO(3)上的** ***伴隨*** **性質。同樣地，在SE(3)上也有伴隨性質：**

$$
\textbf{T} \exp(\boldsymbol{\xi}^\wedge)\textbf{T}^{-1} = \exp((Ad(\textbf{T})\boldsymbol{\xi})^\wedge)
$$

**其中：**

$$
Ad(\textbf{T}) =
\begin{bmatrix}
\textbf{R} & \textbf{t}^\wedge \textbf{R} \\
\textbf{0} & \textbf{R}
\end{bmatrix}
$$

**仿照左擾動的推導，試推SO(3)和SE(3)在右擾動下的導數。**

**搜索cmake的find_package指令是如何運作的。它有哪些可選的參數？為了讓cmake找到某個庫，需要哪些先決條件？**
