# Week3

Notes
---

- 為了把search做得更有效率，我們提到了infromed search(又稱做heuristic search)，跟uninformed search不同的是它有額外考慮其他有利的資訊，再決定search的下一步。

### Best-First Search

- Best-First Search：會有一個evaluation function來決定下一步，這裡的evaluation function又稱做又稱做heuristic。denoted as h(n)，指的就是從node n開始，到最近/最好的目標所需要的cost。兩個例子：
    - Greedy Search: f(n) = h(n)
    - A* Search: f(n) = h(n) + g(n) -> g指的是path cost。
    - 上週提的 Uniform-Cost Search其實也是Best-First Search的一個例子，它就是f(n)=g(n)
    - A* = Greedy Search + Uniform-Cost Search
- Greedy Search:
    - Completeness: No. (As tree search may stuck in infinite loop A->B->A, graph search is complete in finite spaces but not complete in infinite ones.)
    - Optimality: No. (上面都No了這裡就不用討論了)
    - Time Complexity: 指數型成長，O(b^m)。但是好的h(n)可以節省時間
    - Space Complexity: 指數型成長，O(b^m) as it keeps all nodes in memory.
- A* Search:
    - 概念就是到目前為止如果已經花了太多cost的話，就避免展開那個node
    - Completeness: Yes. (除非你有無窮個點它們的f(n)都比f(goal)小，那每次都是無窮地去選那些點)
    - Optimality: Yes. 但必須要看你的 h(n)，下面補充。
    - Time Complexity: 原則上是指數型成長，O(b^ed)，e指的是 relative error，一樣跟h(n)有關，下面補充。
    - Space Complexity: 指數型成長，O(b^d) as it keeps all nodes in memory.
    - relative error = (h* - h)/h*，h*指的是最完美的evaluation function（也就是正解），但我們不可能知道正解是什麼，但假設正解它存在。但是假設我們知道最佳路徑了，其實我們可以對h(n)作弊，把我們已知的最佳path都給上很小的cost，其他都給上無限大的cost，那這樣time complexity不可能就是指數時間了，肯定一下就跑完，所以如果e很小，時間複雜度也就是線性的了。反方向想，假設我們對h(n)所有的node一律給0，那給0等於是沒給，根本沒意義，所以e算出來還是得到1，在時間複雜度維持同樣是指數型。
    - Optimality比較複雜一點，若你想要保證有最佳解，你的h(n)必須要滿足Admissible的條件，如果你使用的是graph search，還另外必須滿足Consistent的條件，Admissible跟Consistent是屬於「充分、不必要」，就是說如果滿足這個條件，那一定保證有最佳解；可是他不是必要條件，如果今天有一個h(n)不滿足這些條件，它有可能有最佳解、也有可能沒有。
- Optimality of A*: 證明。看完可以知道Consistent其實就是三角不等式，兩邊和大於等於第三邊（也有人稱為monotonicity 單調性）

### Memory Bounded Search

- Iterative Deepening A* (IDA*):
    - 其實上面說的search，在時間複雜度雖然很有可能到達指數型，但以現在運算能力這個並不是最大的問題。另外一個更大的問題是空間複雜度。要降低空間複雜度利用的手法就是DFS深度搜尋，A*的演算法我們同樣也可以像前週說的，套用Iterative Deepening的概念，來降低空間複雜度。
    - cutoff 以f(g+h)為準而不是之前所提到的depth當做cutoff。這樣子記憶體可以只使用到線性程度。
    - Completeness跟Optimality：與A*相同。
    - Time Complexity: 與A*相同。
    - Space Complexity: 變成O(bd)。
- Recursive Best-First Search (RBFS):
    - IDA*的缺點就是忘得太快了(記憶體使用太少了)，其實也不需要忘得這麼快。
    - RBFS還是用DFS，它除了會選擇最好的路徑f1，另外還會再保留第二個好的路徑f2，因為萬一f1走下去根本不好的話，還有另外一個f2的選擇。
    - Completeness跟Optimality：與A*相同。
    - Time Complexity: 與A*相同。另外就是如果你的best path常常跳到其他分支轉換的話，時間複雜度相對來說就會變大。
    - Space Complexity: 變成O(bd)。
- Memory-Bounded A*:
    - In a sense, both IDA* and RBFS use too little memory. 因此提出能夠有效利用多一點的記憶體的方法。
    - Memory-Bounded A* 有好幾種演算法，這邊介紹的Simplified Memory-Bounded A* 其實就是去跑A*, 但是當記憶體滿載，就捨棄the worst leaf，然後去存最新產生的node。
    - 當捨棄的時候，還是必須像RBFS一樣保留那個要捨棄掉的路徑f，以便接下來的路如果不好的話，還可以trace back回到捨棄掉的那個路。
- Performace of Admissible Heuristics:
    - 上面提到的演算法效能都會depend on 你的 Heuristics。怎麼產生好的Heuristic然後又要Admissible？
    - 當你有兩個Heuristic h1, h2，要看誰的performance比較好呢？其實我們只要保證對每個node n, h1(n) >= h2(n)的話，那h1就一定比h2好。
    - 證明以上的結論，聽到睡著有機會再重看。但原則上就是另外一個重點：如果你今天有五六個heuristic，其實每個點你都可以去計算這五六個heuristic，然後去取最大的那個。綜合起來可以保證 h = max(h1, h2, ...h6) 得到的h 會比h1, h2,.., h6好。
    - 上面那樣的評估方式其實計算上是麻煩的，我們另外會去計算effective branching factor去評估誰比較好。
    - 結果投影片的那個effective branching factor的表是怎麼算出來的啊？...
- Generating Heuristics:
    - 一個問題其實你可以技術性的把它擁有的一些限制給拿掉(Relaxed Problem)，方便你去產生好的Heuristics。
    - 另外一個方法不把限制拿掉，而是去看它的sub-problem去產生好的Heuristics。也就是我不把問題全部解完，只解完部分的問題而已。我們可以把所有可能組合起來的子問題，個別算完然後將結果儲存起來，這叫做Pattern Databases. 這些存在Pattern Databases的東西可以拿來當作你的Heuristics.
    - 直接相加兩個子問題的Heuristics，得到的新的Heuristic可能會不再成立Addimisle了
    - 但是如果你是考慮子問題A的時候不加上子問題B的步數，考慮子問題B的時候不加上子問題A的步數，然後這樣做一個相加，Addimisle還是會成立的。這個我們稱作 disjoint pattern databases.
    - 第三種方式就是採用經驗法則，這樣就不能保證Addimisle跟Consistent。實際上解決問題我們常使用這種方式。

Sum up
---

![Imgur](http://i.imgur.com/51RC2FT.png)


Quiz
---

### Answer
1. Yes
2. (X) Yes (?) No, h2 dominates h1
3. (X) h* dominates Max(h1, h2) (O) h* dominates h1 and h2
4. We can still find a solution with no optimality guarantee
5. Space complexity is the main advantage
6. 0 ≤ w < 2
7. 0: the uniform-cost search; 1: A∗ search; 2: the greedy best first search
8. (X) No, because adding two admissible heuristics does not guarantee admissibility (O) Yes, because h1 and h2 are both smaller than h*, so this combination is also admissible
9. (X) g(n) = c, c is a constant (O) Costs of all actions are equal, and h(n) = c for all n and c is a constant
10. (X) No (O) Yes

