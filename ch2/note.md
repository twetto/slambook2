# 第二講：初識SLAM

本講大致分兩部分，第一：將SLAM模組化，並分別介紹它們；第二：準備開發環境。

首先破題，Simultaneous Localization And Mapping (SLAM) 包含了Localization(定位)及Mapping(建圖)兩部分；定位理解自身的**狀態**，建圖理解外在的**環境**。爲了理解這些，我們會需要幫機器安裝一些感測器。SLAM中特別強調這台機器要在未知環境工作，所以諸如GPS、軌道這些安裝在環境的約束我們儘量不用，而是強調使用相機。我自己額外希望能把條件約束在使用單目相機上；因爲其尺度不確定性，所以會比雙目或使用光達/紅外線深度感測還要難一點。另外，雖然本書沒有提到，但單目相機若跟IMU做融合的話其實是有解析解(closed-form solution)的(請參考[蘇黎世理工的課程簡報](https://rpg.ifi.uzh.ch/teaching.html)，大概是Lecture 13 - Visual Inertial fusion那邊)，這使得單目恢復尺度成爲可能；雖然我自己用OpenVINS的經驗這個尺度還蠻常被IMU noise影響的(打開方式不對？我沒研究過它初始化是不是用蘇黎世介紹的這套)。

## VSLAM框架

1. **傳感器訊號讀取**: 以相機爲主、IMU/光達/紅外線爲輔的讀取與預處理。這些不同的感測器之間最好可以有硬體同步；如果沒有的話你可能會需要更完備的算法，比方說[連續時域模型](https://youtu.be/_yuZmzJoWUc)之類的。

2. **前端視覺里程計**: 估算相鄰幀間相機的相對運動，以及局部地圖的樣子。因爲只有局部，不可避免地會有累積漂移的情形發生。

3. **後端最佳化**: 接受前端得到的相機姿態跟「其他約束」，對它們做一些事情(最佳化！)以得到全局一致的軌跡跟地圖。透過排除雜訊解決部分累積漂移。

4. **迴環檢測**: 判斷機器人是否達到先前的位置，有的話，把它加到後端的「其他約束」裏面一起大鍋炒。進一步解決累積漂移。

5. **建圖**: 根據定位的結果回推出一張地圖。本書以度量地圖(依幾何關係爲主)來介紹。這些地圖之後可以被其他導航算法利用，像A\*, D\*, RRT 之類的。

## SLAM問題的數學表述

運動方程:

$$
x_k = f(x_{k-1}, u_k, w_k)
$$

$k$爲時間點、$x_k$ 爲機器人位置、$u_k$是傳感器讀數、$w_k$是各種雜訊。

觀測方程:

$$
z_{k,j} = h(y_j, x_k, v_{k,j})
$$

一樣以$k$爲時間點、$y_j$爲第j個路標點、$v_{k,j}$是雜訊。

要將它們具體的表達出來，我們將它們**參數化**(parametrization)。比方說，我可以用極座標表示一個二維上的$x_k$，或者用笛卡爾座標表示$y_j$。這部分會在第三/四/五講詳細介紹。

於是V-SLAM就變成了一個狀態估計問題：*如何通過帶有雜訊的數據來估計內部的、隱藏的狀態變量？*

因爲SLAM是一個非線性非高斯系統，我們大致可以把它分成兩種方式：EKF或非線性最佳化。這部分會在第六講介紹。另外最近有基於等變濾波(equivariant filter)的解法，因爲它**真的是太快了**，所以我想額外詳讀它。不過要閱讀之前需要一些李群跟流形的預備知識，不知道到底能不能懂，唉。

## 實踐

教你怎麼在Linux用CMake做C++的Hello world，跳過。
另外介紹使用KDevelop作爲IDE開發。不過因爲版本跟語言不同，我實際用起來有點卡卡，所以下面用KDev的習題我就不做了。

## 習題

1. 閱讀文獻[@noauthor_survey_nodate]和[@graph__2013]，你能看懂其中的內容嗎？

A: 不太懂，多出了許多沒見過的名詞，像是勻速運動模型、消元啥的。不過我想第一篇主要是比較了各個V-SLAM的優缺點，來幫助看那個適合什麼問題。第二篇是介紹graph-based optimization(繁中有正式譯名嗎？)，之後第九講會提到我就沒細看了。我想主要是來和上面提到的V-SLAM框架互相印證加強印象，目前這樣的理解應該沒問題(吧)。

2. \*閱讀[@cadena_past_2016][@fuentes-pacheco_visual_2015][@boal_topological_2014][@chen_kalman_2012][@chen_recent_2007]。這些文獻中關於SLAM的看法與本書有何異同？

A: 這些東西放第二講會不會太早了一點！？我看過一遍的感覺是這本書的介紹相對滿客觀的。實務上，本書會比較偏向使用非線性最佳化，而不是卡爾曼濾波；我個人會希望我的SLAM可以快一點，目前看起來MSCKF、稀疏直接法、等變濾波都蠻快的，只是不知道它們之間的關係。

3. g++指令有哪些參數？怎麼用參數改生成的程式名？

A: 改名字用`-o <name>`。g++有很多參數，其中我比較常碰到的大概是連結庫跟最佳化方面的參數。像是`-march=armv7-a -mfloat-abi=hard -mfpu=neon -O2 ...`就是Arch Linux ARM在樹莓派上預設的參數，顧名思義就是指定你的CPU架構、有沒有硬體浮點運算、程式支援[NEON](https://developer.arm.com/Architectures/Neon)的話就利用它之類的。

4. 使用build資料夾來編譯你的cmake工程，然後在KDevelop中試試。

A: 略

5. 可以在代碼中添加一些語法錯誤，看看編譯器會出什麼訊息。能看懂g++的錯誤訊息嗎？

A: 可以不要嗎，平常看得夠多了。大部分是type error或沒include到函式庫、各版本函式庫呼叫的語法問題之類的。比方說，Eigen3.3.9跟Eigen3.4.0就曾經讓ROS本身編譯過不了(厲害吧，在Arch上ROS是要自己編譯的)。

6. 如果忘記把庫連結到可執行程式上，編譯會報錯嗎？

A: 直接把`CMakeLists.txt`最後一行`target_link_libraries(useHello hello_shared)`註解掉，在`make`時就會報如下的錯誤：

```
/usr/bin/ld: CMakeFiles/useHello.dir/useHello.cpp.o: in function `main':
/home/twetto/Documents/graduated/slambook2/ch2/useHello.cpp:5: undefined reference to `printHello()'
collect2: error: ld returned 1 exit status
make[2]: *** [CMakeFiles/useHello.dir/build.make:97: useHello] Error 1
make[1]: *** [CMakeFiles/Makefile2:167: CMakeFiles/useHello.dir/all] Error 2
make: *** [Makefile:91: all] Error 2
```

`ld`就找不到`pringHello()`了。

7. \*閱讀〘cmake實踐〙，瞭解cmake的其他語法。

A: 有空再看。(雖然不是語法，參數方面我自己平常會用到的是指定安裝路徑的`-DCMAKE_INSTALL_PREFIX=/usr`。)

8. \*完善Hello SLAM小程式，把它做成一個小庫，安裝到硬碟中。然後新建一個project並使用find\_package調用。

A: 略。基本上要安裝到硬碟的話，就在`make`之後`sudo make install`就好了。如果使用Ubuntu的話，可以考慮使用`dpkg`來維護它(比方說`checkinstall`之類的)；Arch的話寫個PKGBUILD會比較方便用`pacman`管理(可以參考[IQIF的庫](https://github.com/twetto/iq-neuron/blob/master/PKGBUILD))，好處是解安裝比較方便，更新也可以[直接從github上抓下來](https://wiki.archlinux.org/title/VCS_package_guidelines)，太神啦。

9. \*閱讀其他cmake教學材料，例如[https://github.com/TheErk/CMake-tutorial](https://github.com/TheErk/CMake-tutorial)。

A: 看過了，馬上就忘了。慘。

10. 找到KDevelop的官方網站，看看它還有哪些特性。你都用上了嗎？

A: 略

11. 如果在第一講學習了Vim，請試試KDevelop的Vim編輯功能。

A: 略。看了下只找到navigation key binding之類的功能。好像也不是那麼必要？

參考文章：
