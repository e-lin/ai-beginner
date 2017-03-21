# Week4

Notes
---

### Beyound Classical Search

- Meta-heuristic: 只利用附近的資訊（如gradient descent的高度差，進而判斷往哪邊走），包含：Steepest Descent（單點爬山）, Simulated Annealing（單點爬山+random walk）, Evolutionary Computation(多點爬山, 也是余天立老師的專長)
- Non-deterministic Actions: 就是S狀態不再是執行action a之後一定跳到S1狀態，有可能依照機率特性跳到其他S2,S3,等狀態，這裡會介紹AND-OR Search.
- Partial Observations: 前面提到之所以會有Non-deterministic Actions的情況是因為我們對環境狀況不是全盤了解的，這邊會提到以下三種情形下可採取的措施。
    - Sensor-less （這個其實意外地靠譜）
    - With Sensors
    - Unknown Environments
- Black-Box Optimization: 最佳化的其中一種，另外一個叫Glass-Box. 差別就在於我們知不知道那個「目標函式」。Glass-Box雖然知道目標函式，但也未必簡單，如果那個目標函式是好幾百行的函式，那就不像只有一個目標函式可以用微分算各個極值去求最佳解，通常這種時候的Glass-Box我們也傾向去視為一種Black-Box去作最佳化，因為目標函式太複雜了。

### Steepest Descent (Ascent)

- A.k.a Gradient descent.
- Like climing Everest in thick fog with amnesia.
- 當周邊的值「小於等於」現在的值，我們就會選擇移動，因為如果在「等於」的情況下移動，再下一次的周邊值又可以有更加好的值出現，可是我們通常在演算法還會再給一個limit值，因為有可能是在「等於」的情況下移動好幾百次，而附近都沒有更好的值。（你可以想像兩種情況走在平原上，一種就是再多走幾步，隔壁就又有高山可以爬，另外一種就是四周其實都是平原）
- TSP(Travelling salesman problem)問題中，使用 SWAP-2 (任意交換兩點)，來找到最佳路徑。因為任意交換兩點，在圖形上的意義就像是原本交叉的兩條線，交換後會變成兩條不交叉的連線，然後利用三角不等式的原理，證明兩邊和大於等於的三邊，表示照著交換後走的路徑就是最佳路徑。
- SWAP-2可能的情形就是C(n 取 2)* (2! - 1)。2!是任意取2後所有可能的state然後扣掉1（原來的state）。也就是O(n^2)。
- SWAP-3可能的情形就是C(n 取 3)* (3! - 1)。3!是任意取3後所有可能的state然後扣掉1（原來的state）。也就是O(n^3)。這樣的方式有可能找到比SWAP-2還短的路徑，但是相對計算成本也提高。
- Steepest Descent只能保證找到nearest local optimum. 有時候收斂會非常慢(zig-zagging behavior)
- Random restart helps Steepest Descent:
    - Prob. p to succeed.
    - 1/p restarts needed.
    - Cost = cost-of-success + (1-p)/p * cost-of-failure.
    - p指的就是落到能找到nearest local optimum的那個爬山點的機率，假設p是0.1那麼我們就需要 (1/0.1=10)也就是10次去retry才有機會找到那個爬山點，那成本就是 1次成功加上9次((1 - 0.1) / 0.1)的失敗
    - 9次的失敗算法是 (1-p)/p = 1/p - 1
    - 其實可以想成 1*(1-p) + 2*(1-p)^2 + 3*(1-p)^3 + ... 一次失敗+兩次失敗+三次失敗 加到無窮項再利用代數的技巧也可以得到上面的式子。

### Simulated Annealing

- 這是為了克服Steepest Descent只能找到nearest local optimum的缺點。
- 它會有一個T參數，T會隨著時間越來越小，另外還會有個E，就是現在的位置代入目標函式後得到的值再去扣掉鄰近的點代入目標函式後的鄰居值，E如果大於零而且是（差距）最大的，表示接下來要走的鄰居是很好的，在Steepest Descent就會選擇它。但E如果小於零，在這裡我們並不會就捨棄這個鄰居，而是另外去求一個機率e^(E/T), 同樣把這個鄰居納入考量，random walk可能會選那些機率比較低的點，它不像Steepest Descent只納入最好的值。得到的e^(E/T)剛好會介於0~1之間，因為E/T會是負的。然後當T越來越小，就會是e的負無窮大，那得到的機率就剛好是零，也就不再會去選了。([Exponentiation Wiki][R1])
- 所以Simulated Annealing在初期的表現是random walk，有點亂跳亂走的感覺，但越到後期，就不會再選擇其他機率比較低的點，行為上就比較像Steepest Descent了。
- 有很多相同的演算法差別只在於怎麼去減少你的T，不一定只是隨著時間越來越小而已。
- 數學上可以證明當T減少的速度非常慢，可以保證Simulated Annealing求得到global optimum (but not practically useful...)，可是在有限的時間下，也不一定保證可以得到global optimum就是了。

### Local Beam Search

- 上面兩種都是單點爬山，這裡是多點爬山。
- keep best k states instead of just one.
- 只丟一個點，利用上面的方法，然後重複跑十次；跟這裡的一次丟十個點去跑，差別在哪裡？
- 一次keep k的點，其實這些點會有交互作用，隨時間演變，所有的state會趨向最好的那個state的附近。
- 缺點是有可能「看似好的點」，結果群聚效應大家都往那裡發展，而真正最好的點如果一開始沒有被選到，就被忽略了。
- Stochastic Beam Search 就不是選最好的k個，而是隨機選k個。這樣可以減少群聚效應，讓點不要聚集得太快，但是也有可能讓點非常分散，這延伸出很多trade off 以及研究的課題。

### Evolutionary Computation

- 演化式計算，借重達爾文演化論。Darwin's idea:
    - Natural selection
    - Struggle to survive
    - Survial of the fittest
    - Genetic variation: inherited traits(genes) -> Use crossover and mutation as a genetic algo.
- Simple Genetic Algorithm, SGA(基因)遺傳演算法:
    - Encoding
    - Initialization
    - Evaluation: apply the fitness function (適者生存)
    - Selection
    - Recombination: crossover and mutation. (90年以後GA的研究也未必只有這兩種組合)

### Non-deterministic Action
    - 沒有sensor的情況下，列出所有可能的狀態(belief states) --> AND-OR Search
    - 實作AND-OR Search就是利用遞迴。

### Partial Observations
    - 有sensor的情況下，又可以將所有可能的狀態shrink成比較少的belief states。
    - 應用：localization in a maze.

### Online Search with Unknown Environments
    - 使用Learning Real-Time A* (LRTA*)
    - Unlike A*, LRTA* is NOT complete for infinite state spaces.
    - With n states, LRTA* guarantees to find optimum within O(n^2) steps, but usually much faster.
    - LRTA*最大的問題就是記憶體。因為有太多state記不住，我們通常會把state對應到另一個feature domain, 當map後的feature是一樣的話就視作同一個state.


Sum up
---

![Imgur](http://i.imgur.com/ZsoLW6B.png)



Quiz
---

### Answer
1. (X) a: greedy best first search; b: hill climbing; c: genetic algorithm (O) a: hill climbing; b: hill climbing; c: random walk
2. In the AND-OR search, AND node only needs to reach goal state in one of the leaves, OR node needs to reach goal state in all leaves.
3. Yes
4. (X) Repeated trials (O) Need admissible heuristic at beginning
5. 6
6. (X) 1x (X) 2x (O) 3o

1x:
![Imgur](http://i.imgur.com/0HyLILF.png)

2x:
![Imgur](http://i.imgur.com/rJhVq4Y.png)

3o:
![Imgur](http://i.imgur.com/802AXao.png)

7. 3
8. No


[R1]: https://zh.wikipedia.org/wiki/%E5%86%AA
