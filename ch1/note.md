# 第一講：預備知識

因爲本講只是個類似緒論的東西，也沒有什麼代碼，所以在[原倉儲](https://github.com/gaoxiang12/slambook2)中是沒有ch1這個資料夾的；不過，既然它是單獨的一講，必然有它的道理，所以我們額外做了這個筆記。另外，因爲 GitHub 在[最近幾天支援了 LaTeX 風格的數學表達式](https://github.blog/2022-05-19-math-support-in-markdown/)，直接把筆記用 MarkDown 寫在這裡也變得方便。

首先說明我爲什麼要學SLAM。其實原因也很簡單，就是研究上會用到。我們的目標是在嵌入式系統上達到避障的效果，再加上一些額外的限制，像是FPS、硬體設定、運算平臺等等。或許在現在這個時間點，這是已經做得到的事情，但如果要以此爲基礎繼續朝**超低功耗平臺**、**微秒級快速反應**、**神經形態計算**等等的方向發展的話，把這本書的內容踏實地學一遍是必要的。

本講中預先告知了我們會用到一些第三方函式庫，像是 Eigen、OpenCV、PCL、g2o、ceres 之類的。雖然我在過去做研究的時候就因爲使用 ROS 的經驗而把上面的庫幾乎裝了一遍，但實際呼叫過函式的只有 OpenCV 而已，所以我其實還滿開心能透過這次挑戰重新認識它們。

本講除了講接下來教什麼之外，還對我們要求了一些**預備知識**（終於點題了）：

1. 高等數學、線性代數、機率

2. C++ 基礎

3. Linux 基礎

高博在此頁中註解到「*每個人都至少需要學三遍線性代數：本科一遍、研究生一遍、工作時期一遍*」，我看得真是汗顏，因爲我研究所時期基本沒用到啥線代，心裡是一個苦啊。本講最後的習題部分第一題我甚至是去看[臺大蘇老師的開放式課程](http://ocw.aca.ntu.edu.tw/ntu-ocw/ocw/cou/102S207)才回答出來的。唉，苦啊。

另外，我會對一些中國慣用語跟譯名做一些調整，希望臺灣的同學們讀起來比較順暢（也有可能適得其反，因爲大家術語都講英文不翻成中文了，哈哈）

## 習題（基本知識自測題）

1. 已知A, b, 如何求解Ax=b?對A, b有哪些要求？提示：從A的維度(dimension)和秩(rank)來分析。

A: 可用高斯消去法求解。條件是，把$A$, $b$ 寫成 $\begin{bmatrix}A|b\end{bmatrix}$ 的 augmented matrix 的樣式的話，這個矩陣必須是 consistent 的，因爲 consistent 代表這個系統有一組或無限多組解。要怎麼求consistency？如果$b$是$A$的linear combination的話就有解，而這可以靠高斯消去法得到reduced row echelon form 來簡單看出來(這同時也表示，$rank(A)$跟$rank(\begin{bmatrix}A|b\end{bmatrix})$必須是一樣的才有解)。有幾組解？想要知道它有幾組解，我們可以求這個$\begin{bmatrix}A|b\end{bmatrix}$的rank跟nullity。如果它是consistent且nullity爲零，代表它有一組解；如果它是consistent且nullity不爲零，則有無限多解。

2. 高斯分佈是什麼？它的一維形式是什麼樣子？它的高維形式是什麼樣子？

A: 一個隨機變數$X \sim N(\mu,\rho^2)$的機率密度函數爲

$$
p(x) = \frac{1}{\sqrt{2\pi}\rho}exp(-\frac{1}{2}\frac{(x-\mu)^2}{\rho^2})
$$

其高維形式$X = \begin{bmatrix}X_1, \dots, X_N\end{bmatrix}^T$爲

$$
p(x) = \frac{1}{\sqrt{(2\pi)^N det(\boldsymbol{\Sigma})}} exp(-\frac{1}{2} (\boldsymbol{x}-\boldsymbol{\mu})^T \boldsymbol{\Sigma}^{-1} (\boldsymbol{x}-\boldsymbol{\mu}))
$$

其中$|\boldsymbol{\Sigma}|$爲共變異數矩陣的行列式。（複習：共變異數的定義是$cov(X,Y)=E((X-\mu)(Y-\nu))=E(X\cdot Y)-\mu\nu$。）

3. 你知道 C++ 中的**類別**嗎？你知道 STL 嗎？你使用過它們嗎？

A: 我在寫 [IQIF 模擬器](https://github.com/twetto/iq-neuron)的時候有用過一些基本的類別。特別在撰寫神經連結時我曾試用過`forward_list`，不過沒我用手刻的linked list 快，最後就沒用了。後來我才發現應該使用`vector`來實現adjacency list，不過還沒做，看看以後有沒有空弄吧。編：我用了，沒比較快，是要怎樣才能快啊？不懂。

4. 你以前怎樣書寫 C++ 程式？（你完全可以說只在 Visual C++ 6.0 下寫過 C++ 工程，只要你有寫 C++ 和 C 語言的經驗就行。）

A: 我用 Vim 打字，然後用 CMake 來編。基本上還是上面IQIF實作時弄的。

5. 你知道 C++11 標準嗎？你聽說過或使用過其中哪些新特性？有沒有其他的標準？

A: 窩不知道... C++11 有很多東西，我想還是要看在解決問題時會用到哪些新特性才比較好回答，現在窩不知道。

6. 你知道 Linux 嗎？你有沒有至少使用過一種（不算安卓）發行版，比如 Ubuntu ？

A: 我主要用過 Debian-based 跟 Arch-based 發行版。

7. Linux 的目錄結構是什麼樣的？你知道哪些基本指令，比如 ls, cat 等？

A: Linux 的目錄結構是由 Filesystem Hierarchy Standard (FHS) 標準決定的，可以參考[鳥哥的解說](https://linux.vbird.org/linux_basic/centos7/0210filepermission.php)。基本指令很多，就不講了，之前有試著[介紹過Linux](https://docs.google.com/presentation/d/1v3A93YzpIGmUnXliisIgyobbJm_SWueY05pVlhzLSJ8/edit?usp=sharing)，有空可以看看能改進什麼地方。

8. 如何在 Ubuntu 中安裝套件？這些套件被安裝在什麼地方？如果只知道模糊的套件名稱（比方說想要裝一個名稱中含有Eigen的庫），應該如何安裝？

A: `apt install <pkgname>`。要看安裝在什麼地方可以用`dpkg -L <pkgname>`。`apt list | grep -i eigen`看看有沒有搜到類似於`libeigen-dev`的包吧。編：查了一下叫做`libeigen3-dev`

9. \*花一個小時學習 Vim ，因爲你遲早會用它。你可以在終端中輸入 vimtutor 閱讀一遍所有內容。我們不需要你非常熟練地操作它，只要能夠在學習本書的過程中使用它輸入代碼即可。**不要在它的插件上浪費時間，不要想着把 Vim 用成 IDE ，我們只用它做文字編輯的工作**。

A: 我原本就在用Vim了，不過我沒用過 vimtutor，所以我乖乖去讀了一次，又被我學到好幾招（還沒學完就是了）。
