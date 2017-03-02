# Week2

Notes
---

- Tree Search 跟 Graph Search 的差別在：Tree Search 當於到重複的state的時候還是會繼續跑。所以一個用Graph Search只有 N state的問題，用Tree Search的話就會增加到 2^N 個state。電腦沒有能力去運算指數級複雜度的問題。即便我們用電腦去紀錄這些state（explored state），然後去檢查是不是出現重複的state了，但是像是西洋棋的問題state數量可以10^40次方，這個例子來說電腦就沒有足夠的記憶體可以記錄。在有限記憶體的情況下，我們可以利用hash table的技術去檢查重複state的問題。（即便hash table可能因為collision的因素造成檢查結果不是太準確）

- 我們會在最後才去做goal test，也就是去檢查這個狀態是不是goal state。在最後才檢查的原因是為了確保得到最佳解，因爲如果search做到一半遇到了goal stater就停下來了，那不能保證一定是最佳解。

- 若有記憶體限制的問題，使用Tree Search。反之使用Graph Search（因為perfromance較好）。Frontier的選擇可以使用FIFO(Queue)、LIFO(Stack)或Priority Queue(使用Heap實作)。

- 評估Search演算法的效能有四個面向：Completeness, Optimality, Time & Space Complexity。完整性是說只要存在一個解，是否一定可以找到那個解。最佳性有分兩種觀點：1. 如果有很多個goal state，是否能找到cost最小的解；2. 如果只有一個goal state，但是走到這個goal有很多種走法（所謂一個解：就是a sequence of actions），是否能找到最短路徑的解。而另外時間跟空間的複雜度，使用到三個因素評量：1. b - maximum branching factor of the search tree. 指的是所有的node當中，最多的分支數。2. d - depth of the least-cost solution. 指的是最佳解的深度(goal的位置)。3. m - maximum depth of the state space (may be infinite)指的是可能的state裡面，最深的深度（有可能會是無窮大）。

- BFS:
    - Completeness: Yes. (if b is finite) 如果分支數無限的情況下，永遠沒有機會展開全部，那完整性就不成立。
    - Optimality: No. 因為BFS找到的解一定都是最淺的那個，有可能深度較深的解才是最佳解。除非保證path cost是跟深度呈正相關，保證找到最淺的解，所對應的path cost也是最小的情況下，BFS才算有成立最佳性。
    - Time Complexity: O(b^d) (展第一層要1，第二層b，第三層b^2...到b^d) 或是 O(b^(d+1)) if goal test is applied after expansion. 也就是多一層，不過差別很小。
    - Space Complexity: O(b^d) (keeps every node in memory)
    - 以現在電腦運算能力一秒可以長10^9次方個node來看的話，假設一個node只佔了若干個bytes，算下來也需要 100MB/sec 來儲存這些node。so 24hrs = 8640GB 是很可怕的事情。也就是說 Space Complexity 我們特別注重。即使可以把電腦跑一天運算也沒差，但是記憶體一下就爆掉了。

- Uniform-Cost Search:
    - 是BFS的概括，Uniform-Cost Search規定在展開的時候，優先選cost最小的那個展開。如果選擇每個Frontier的cost都是1的話，那它就是一個BFS。實作上就是使用Priority Queue。
    - 如果你是用Tree Search的方式展開的話，就算展到goal state也會被放到Frontier裡面，最後做goal check的時候可以保證拿到的解是最佳解；但如果是用Graph Search的方式展開的話，要記得利用Priority Queue的DecreaseKey函式去更新最小cost值。
    - Completeness: Yes. (step cost needs > 0)
    - Optimality: Yes (因為展開的次序都是從最小的cost開始展)
    - Time & Space Complexity: 跟BFS一樣都是 exponential 的複雜度

- DFS:
    - 實作上最常使用遞迴的寫法。如果要用queue的寫法的話，就是使用FILO Queue（Stack）。(其實就算我們用遞迴去寫，電腦那邊還是會用stack的方式去處理遞迴。)
    - Completeness: No. Fails in infinite-depth spaces, spaces with loops.
    - Optimality: No. 上面都No了這個就沒什麼好談的。
    - Time Complexity: O(b^m)，簡單來記就是指數型成長。m就是指整棵樹最深的深度，如果m比d(goal的位置)大很多，那整個運算時間會很長。
    - Space Complexity: O(b*m)，linear space! 這是DFS唯一的優點....如果使用Backtracking的方法，每次探索下去一層都先不紀錄分支b的話（等到回來到本層再去展開分支），那這樣就只需要O(m)的空間複雜度而已。

- DLS (Depth-Limited Search):
    - DFS never terminates if m is infinite.
    - DLS = DFS with depth limied l.
    - Nodes at depth l have no successors.
    - 實作上使用遞迴的寫法。
    - Completeness: Yes. No if l < d.
    - Optimality: No. Not optimal in general. (even if l > d)
    - Time Complexity: O(b^l)
    - Space Complexity: O(b*l)

- Iterative-Deepening Search (IDS):
    - 改進DLS。今天 l < d （goal 的深度 d 比limited l 還要深）的情況下，會讓DLS不具有Completeness。所以我們想要漸進地把 l 的限制放寬。也就是一個for loop: for l = 0 ~ infinite之下，一個一個去跑DLS。內部一個一個去跑DLS的原因是因為我們需要利用DLS的低空間複雜度的優點。
    - Completeness: Yes.
    - Optimality: No. (Yes only if step cost = 1)
    - Time Complexity: d*b^1 + (d-1)*b^2 +  ... + b^d= O(b^d) (最深的地方只會做一次 + 倒數第二深的地方要做兩次 + ... 最淺的那個起點每次跑for loop: for l = 0~的時候 都一定會做一次，共會做d次)
    - Space Complexity: O(b*d)
    - IDS看起來好像很浪費時間，我們實際套用數字計算一下。假設b=10, d=5, goal at far right leaf.
        - N(IDS) = 50 + 400 + 3,000 + 20,000 + 100,000 = 123,450
        - N(BFS) = 10 + 100 + 1,000 + 10,000 + 100,000 = 111,100
    - Repeated search in IDS is not severe as you think.
    - In general, IDS is preferred when search space is large and depth is unknown.
    - IDS is advantage in adversarial search. (future talk.)
    - If we change all step costs to 2 in IDS, the optimality is also guaranteed.

Sum up
---

![Imgur](http://i.imgur.com/crPFJQG.png)

![Imgur](http://i.imgur.com/G2gHlLH.png)


Quiz
---

### Answer
1. (X) 14 (O) 15
2. 11
3. BFS: Yes; DFS: No; UCS: No
4. The depth-Limited search
5. 14
6.
    - (X)Because BFS only returns the shortest depth solution, and the uniform-cost search only does the greedy decision.
    - (O) Any path, no matter how bad it appears, might lead to an arbitrarily large reward (negative cost). Therefore, we would need to exhaust all possible paths to ensure to find the optimal path.
7. Simply explore the entire state space
8.
    (X) Completeness: Yes; optimality: Yes
    (O)Completeness: Yes; optimality: No
9. Time complexity: O(b^d); space complexity: O(bd)

