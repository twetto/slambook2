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
\end{bmatrix} \triangleq
\textbf{Ra}'
$$

$\textbf{R}$就是旋轉矩陣了。

## 平移

## 實踐：Eigen
