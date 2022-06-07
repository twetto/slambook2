# 第三講：三維空間剛體運動

本講介紹如何用數學描述剛體在三維空間的運動。
讀之前有件事特別強調，大陸那邊行列是相反的（或者臺灣比較不一樣？）；這個筆記以直行橫列爲主。本書的向量爲行向量。

## 旋轉

外積表示方式($\textbf{e}$爲基底)：

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
\textbf{a} \wedge \textbf{b}
$$

## 平移

## 實踐：Eigen
