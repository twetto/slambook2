# 第三講：三維空間剛體運動

本講介紹如何用數學描述剛體在三維空間的運動。
讀之前有件事特別強調，大陸那邊行列是相反的（或者臺灣比較不一樣？）；這個筆記以直行橫列爲主。本書的向量爲行向量。

## 旋轉

**外積表示方式($\textbf{e}$爲基底)：**

$$
\textbf{a} \times \textbf{b} =
\begin{Vmatrix}
\textbf{e}_1 & \textbf{e}_2 & \textbf{e}_3 \\
a_1 & a_2 & a_3 \\
b_1 & b_2 & b_3
\end{Vmatrix} =
\begin{bmatrix}
a_2 b_3 - a_3 b_2 \\
a_3 b_1 - a_1 b_3 \\
a_1 b_2 - a_2 b_1
\end{bmatrix} =
\begin{bmatrix}
0 & -a_3 & a_2 \\
a_3 & 0 & -a_1 \\
-a_2 & a_1 & 0
\end{bmatrix} \textbf{b} \triangleq
\textbf{a}^\wedge \textbf{b}
$$

其中$\textbf{a}^\wedge$爲反對稱矩陣($\textbf{A}^T = -\textbf{A}$)；它跟$\textbf{a}$是對射(或稱一一映射, bijection)關係。

**旋轉的歐氏變換:**

兩個座標系$\textbf{e}', \textbf{e}$對於同一個點$\textbf{a}$的表示:

$$
\begin{bmatrix} \textbf{e}_1 & \textbf{e}_2 & \textbf{e}_3 \end{bmatrix}
\begin{bmatrix}
\textbf{a}_1 \\
\textbf{a}_2 \\
\textbf{a}_3
\end{bmatrix} = 
\begin{bmatrix} \textbf{e}'_1 & \textbf{e}'_2 & \textbf{e}'_3 \end{bmatrix}
\begin{bmatrix}
\textbf{a}'_1 \\
\textbf{a}'_2 \\
\textbf{a}'_3
\end{bmatrix}
$$

同時左乘$\begin{bmatrix} \textbf{e}^T_1 & \textbf{e}^T_2 & \textbf{e}^T_3 \end{bmatrix}^T$:

$$
\begin{bmatrix}
\textbf{a}_1 \\
\textbf{a}_2 \\
\textbf{a}_3
\end{bmatrix} = 
\begin{bmatrix}
\textbf{e}^T_1 \textbf{e}'_1 & \textbf{e}^T_1 \textbf{e}'_2 & \textbf{e}^T_1 \textbf{e}'_3 \\
\textbf{e}^T_2 \textbf{e}'_1 & \textbf{e}^T_2 \textbf{e}'_2 & \textbf{e}^T_2 \textbf{e}'_3 \\
\textbf{e}^T_3 \textbf{e}'_1 & \textbf{e}^T_3 \textbf{e}'_2 & \textbf{e}^T_3 \textbf{e}'_3
\end{bmatrix}
\begin{bmatrix}
\textbf{a}'_1 \\
\textbf{a}'_2 \\
\textbf{a}'_3
\end{bmatrix} \triangleq
\textbf{Ra}'
$$

$\textbf{R}$就是旋轉矩陣了。這是一個正交矩陣，它的逆（同時也是轉置）描述了一個相反的旋轉。

SO(n) = 特殊正交群, $RR^T = I, det(R) = 1$

**旋轉向量--羅德里格斯公式**: 用單位向量+旋轉角度$\theta$也可表示旋轉。有奇異性。

(這裏有公式)

**歐拉角**: 最直觀的yaw-pitch-roll(rpy, ZYX方向)旋轉角。有萬向鎖問題，不適合做計算，只適合debug時方便人類理解。也有奇異性。

**四元數**: 沒奇異性，也不像旋轉矩陣那麼大。不過機制比較複雜。大部分VIO算法在做pre-integration時都使用四元數。

四元數可以看作複數的延伸，只不過虛部從一個i變三個i, j, k。

i, j, k 相乘的規則是這樣：

$$
ii = jj = kk = -1
ij = k, ji = -k
jk = i, kj = -i
ki = j, ik = -j
$$

**各個旋轉表示法之間的轉換**: 詳見習題

## +平移

$$
\begin{bmatrix}
\textbf{a}' \\
1
\end{bmatrix} =
\begin{bmatrix}
\textbf{R} & \textbf{t} \\
\textbf{0}^T & 1
\end{bmatrix}
\begin{bmatrix}
\textbf{a} \\
1
\end{bmatrix} \triangleq
\textbf{T}
\begin{bmatrix}
\textbf{a} \\
1
\end{bmatrix}
$$

R, t 構成了 SE(3) (詳細就不講了，打數學式子好麻煩)

$$
\textbf{T}^{-1} =
\begin{bmatrix}
\textbf{R}^T & -\textbf{R}^T\textbf{t} \\
\textbf{0}^T & 1
\end{bmatrix}
$$

## 實踐：Eigen

(照著這個資料夾的code跑了一遍。)
本講有用到C++11, 但不知爲何傳統的`set(CMAKE_CXX_FLAGS "-std=c++11")`在我機器上不能用。改用CMake 3.1後支援的語法`set(CMAKE_CXX_STANDARD 11)`就可以啦。剩下的小心得寫在註解裡。

## 習題

**驗證旋轉矩陣是正交矩陣。**

A: 我們先來看什麼是正交矩陣。維基百科說：「_正交矩陣（英語：orthogonal matrix）是一個方塊矩陣_$Q$_，其元素為實數，而且行向量與列向量皆為正交的單位向量，使得該矩陣的轉置矩陣為其逆矩陣_」，比照上面對特殊正交群SO(3)的定義即可很明顯看出旋轉矩陣定義符合正交矩陣定義。從另一個角度想，如果旋轉矩陣不是正交矩陣的話會很麻煩，這表示這個旋轉的旋轉軸變了方向，而這是不被允許的(題外話，SO(n)的另一個要求det(1)是爲了保留向量大小(旋轉角度))。第一次接觸時，可能會對旋轉軸的方向沒什麼實感。旋轉矩陣跟旋轉軸的對應關係可以用羅德里格斯公式看出，轉軸是旋轉矩陣特徵值1對應的特徵向量。因爲特徵值跟特徵向量是對射關係(一對一)，所以轉軸是不會變的。

**\*尋找羅德里格斯公式的推導過程並加以理解。**

A: 具體請參考[維基百科的頁面](https://en.wikipedia.org/wiki/Rodrigues%27_rotation_formula)。推導的思路不難，就是把想要旋轉的向量分解成平行跟垂直於旋轉軸的分量，再用旋轉軸跟旋轉角度去分別表示它們再相加。平行的部分旋轉時不變，而垂直部分則另外用「垂直分量本身當作第一基」跟「旋轉軸單位向量跟垂直分量的外積當作第二基」，進行極座標的轉換。

維基頁面中還有另外提到[向量三重積](https://en.wikipedia.org/wiki/Triple_product#Vector_triple_product)，但根據[這篇](https://hackmd.io/@RintarouTW/Rodrigues%E2%80%99_Rotation_Formula)其實也用不到。

**驗證四元數旋轉某個點後，結果是一個虛四元數（實部爲零），所以仍然對應到一個三維空間點。**

A: 四元數$\textbf{q}$旋轉點$\textbf{p}$成點$\textbf{p}'$是這樣：

$$
\textbf{p}' = \textbf{q}\textbf{p}\textbf{q}^{-1}
$$

設$\textbf{p} = [0,p_x,p_y,p_z]^T$, $\textbf{q} = q_s + q_xi + q_yj + q_zk$，開始旋轉：

$$
\begin{align}
\textbf{p}' &= \textbf{q}\textbf{p}\textbf{q}^{-1} \\
&= \textbf{q} (0 + p_xi + p_yj + p_zk) (q_s - q_xi - q_yj - q_zk) \\
&= \textbf{q} (p_xiq_s - p_xiq_xi - p_xiq_yj - p_xiq_zk
             + p_yjq_s - p_yjq_xi - p_yjq_yj - p_yjq_zk
             + p_zkq_s - p_zkq_xi - p_zkq_yj - p_zkq_zk) \\
&= \textbf{q} (p_xq_x  + p_yq_y  + p_zq_z
             + p_xq_si - p_yq_zi + p_zq_yi
             + p_yq_sj + p_xq_zj - p_zq_xj
             + p_zq_sk - p_xq_yk + p_yq_xk) \\
&= (q_s + q_xi + q_yj + q_zk) (p_xq_x  + p_yq_y  + p_zq_z
                             + p_xq_si - p_yq_zi + p_zq_yi
                             + p_yq_sj + p_xq_zj - p_zq_xj
                             + p_zq_sk - p_xq_yk + p_yq_xk) \\
&= q_sp_xq_x  + q_sp_yq_y  + q_sp_zq_z + q_sp_xq_si - q_sp_yq_zi + q_sp_zq_yi + q_sp_yq_sj + q_sp_xq_zj - q_sp_zq_xj + q_sp_zq_sk - q_sp_xq_yk + q_sp_yq_xk \\
 &+ q_xip_xq_x  + q_xip_yq_y  + q_xip_zq_z + q_xip_xq_si - q_xip_yq_zi + q_xip_zq_yi + q_xip_yq_sj + q_xip_xq_zj - q_xip_zq_xj + q_xip_zq_sk - q_xip_xq_yk + q_xip_yq_xk \\
 &+ q_yjp_xq_x  + q_yjp_yq_y  + q_yjp_zq_z + q_yjp_xq_si - q_yjp_yq_zi + q_yjp_zq_yi + q_yjp_yq_sj + q_yjp_xq_zj - q_yjp_zq_xj + q_yjp_zq_sk - q_yjp_xq_yk + q_yjp_yq_xk \\
 &+ q_zkp_xq_x  + q_zkp_yq_y  + q_zkp_zq_z + q_zkp_xq_si - q_zkp_yq_zi + q_zkp_zq_yi + q_zkp_yq_sj + q_zkp_xq_zj - q_zkp_zq_xj + q_zkp_zq_sk - q_zkp_xq_yk + q_zkp_yq_xk \\
&= q_sp_xq_x + q_sp_yq_y + q_sp_zq_z - q_xp_xq_s + q_xp_yq_z - q_xp_zq_y - q_yp_yq_s - q_yp_xq_z + q_yp_zq_x - q_zp_zq_s + q_zp_xq_y - q_zp_yq_x \\
 &+ 一堆虛部 \\
&= 只剩虛部(我好懶)
\end{align}
$$

或者也可以用向量形式計算：

$$
\begin{align}
\textbf{p}' &= \textbf{q}\textbf{p}\textbf{q}^{-1} \\
&= \textbf{q} [s_ps_q-\textbf{v_p}^T\textbf{-v_q}, s_p\textbf{-v_q} + s_q\textbf{v_p} + \textbf{v_p} \times \textbf{-v_q}]^T \\
&= \textbf{q} [s_ps_q+\textbf{v_p}^T\textbf{v_q}, -s_p\textbf{v_q} + s_q\textbf{v_p} - \textbf{v_p} \times \textbf{v_q}]^T
\end{align}
$$

這邊我們偷懶(又愉快)地假設$\textbf{q}$是一個單位四元數，這樣它的逆就等於它的共軛。

**畫表總結旋轉矩陣、軸角、歐拉角、四元數的轉換關係。**

A: 轉換關係如下：

旋轉矩陣 <-> 軸角: 羅德里格斯公式(式3.15, 3.17)

旋轉矩陣 <-> 四元數: 式3.39

四元數 <-> 軸角: 式3.44

至於歐拉角，我會習慣先轉成矩陣再轉成別的，以rpy角爲例，具體方式參考[維基](https://zh.wikipedia.org/zh-tw/%E6%97%8B%E8%BD%AC%E7%9F%A9%E9%98%B5#%E4%B8%89%E7%BB%B4%E7%A9%BA%E9%97%B4)。

**假設有一個大的Eigen矩陣，想把它的左上角** $3\times3$ **的塊取出來，然後賦值爲**$\textbf{I}_{3\times3}$**。請編程實現。**

A: 可以使用`block()`取出：

```cpp
Matrix<double, 10, 10> matrix_10_10 = MatrixXd::Random(10, 10);
matrix_10_10.block(0,0,3,3) << MatrixXd::Identity(3, 3);
```

具體我實作在[範例程式](./useEigen/eigenMatrix.cpp)裡。

需要注意的是，在使用`vector`相關的函式，例如像`dot()`的時候，要儘量避免使用`block()`，改爲使用`head()`, `tail()`, `segment()`；不然會出現`static_assert failed`的情況。我曾經在編譯[OpenVINS](https://github.com/rpng/open_vins)的時候遇到[類似的問題](https://github.com/rpng/open_vins/commit/c89f14f143e2058e9201025453b9dd07b443a49d)。

**一般線性方程$\textbf{Ax}=\textbf{b}$有哪幾種做法？你能在Eigen中實現嗎？**

A: 我假設是指解方程組。參考[官方文檔](https://eigen.tuxfamily.org/dox/group__TutorialLinearAlgebra.html)，大致能分成LU分解、QR分解、Cholesky分解、SVD分解。要選那一種要看方程本身是否超定/欠定(用SVD)、正定/負半定、方程大小、對精度/速度的tradeoff、是否良置/病態。
