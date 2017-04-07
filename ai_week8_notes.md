# Week8

Notes
---

- Planning Domain Definition Language (PDDL)。使用PDDL來結構化地歸納一個問題，可以很簡單的產生heuristic 函式，然後再套用Search algo去解決問題。
- 我們也可以利用 Planning Graph 去輔助我們產生heuristic
- PDDL 跟 FOL 的表達能力都很強，但是只有PDDL 可以推導出一個 domain-independent heuristic

### Complexity of Planning
![Imgur](http://i.imgur.com/BUa3Ugd.png)

- 可以利用下面兩種方式做Search:
    - Forward (Data driven)
    - Backward (Goal driven)
- Forward Search 雖然在過程中會導出很多無用的推論（如果你是使用 uninformed search的話，會長出一堆無用的分支），但是一個好的domain-independent heuristic可以很容易導入到Forward Search（採用informed search的話），現今一般系統都偏好使用Forward Search，也比backward來得直覺。

- 要得到好的heuristic 就是這個heuristic 要admissible, 也就是估計出來的 cost 永遠比 lowest possible cost 少（不高估），簡單就是把問題relax化。因為簡化過的問題要花的cost一定比原本複雜的問題少。兩種方式簡化：
    - 增加edge:
        - 一種做法是 ignore precondition, np-hard問題，所以我們用greedy log n-approximation algorithm 去找接近的解 (雖然不能保證admissibility 但是大部分情況都是admissible的)
        - 另一種做法是 ignore delete lists, np-hard問題, 改用hill climber去找一個接近的解 (多項式時間解決)
    - 合併nodes

### Planning Graph
- 利用 Planning Graph 產生的heuristic可以保證admissible, 而且時間複雜度也不高
- 其實你也可以直接在Planning Graph 套用search演算法(GRAPHSEARCH)而不是在PDDL上套用

### Complexity of Planning Graphs
![Imgur](http://i.imgur.com/tcMynnv.png)

### Planning Graphs for Heuristics
- 我們的goal可能有好幾個subgoal, 要估計多個subgoals他們的cost, 有以下三種挑選heuristic的方法：
    - Max level: 就是選最大cost的那個subgoal當作heuristic
    - Level sum: 所有subgoal的總和當作heuristic
    - Set-level: 挑選出的subgoal兩兩都沒有mutual, 然後又可以包含整個goal
    - 或者用 serial planning graph 讓估計更準確

![Imgur](http://i.imgur.com/bd5ShAu.png)


Sum up
---
![Imgur](http://i.imgur.com/Wxs5H97.png)


Quiz
---

### Answer
1.　If negative effects and negative preconditions are disallowed, PlanSAT is polynomial.

