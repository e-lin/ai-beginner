# Week5

Notes
---

### Adversarial Search: Game Playing
- 通常我們講的Adversarial Search就是指競爭關係的遊戲。
- 然後假設是兩個玩家、零和的情況下，可以使用 MinMax(Negamax) Search

### MinMax(Negamax) Search
- 實作就是用DFS
- Completeness: Yes, if tree is finite.
- Optimality: Yes, against an optimal opponent. Otherwise?
    - Risky moves that leads to complicated variations might be better to revert unfavored situations.
- Time Complexity: O(b^m)
- Space Complexity: O(bm)(DFS); or O(m) if the algorithm generates actions one at a time.

### alpha-beta Pruning
- 實作就是用DFS
- 因為在MinMax(Negamax) Search其實不需要每個節點都去檢查。第一篇提出這方法的論文就是定義了一個範圍[alpha, beta], 在這範圍之內的節點才值得去探索，以外的全部pruning掉。
- Min修改beta, Max修改alpha.
- 在跑DFS時，會隨時去修改[alpha, beta]這個window(範圍)。當跑到某個節點，而修改後的window產生交錯時（beta小於alpha），就表示接下來不管看到什麼節點，都不會影響目前的結果，也就可以直接pruning掉。
- Efficiency of alpha-beta Pruning:
    - Highly depends on the order of moves.
    - Worst case: no pruning -> O(b^m)
    - Best case: always check the best move first.
        - still need to check every move for the 1st player.
        - only need to check one move for the 2nd player.
        - O( b * 1 * b * 1 * ...) = O(b^(m/2))
    - Average case: O(b^(3m/4))
- 在討論演算法效能時我們通常只看Worst case跟Avergae case(因為best case通常比較難發生)，但在alpha-beta Pruning裡面其實只要使用iterative deepning的方式，搭配好的huristic評估函式，進而去優先選擇走哪個節點，這樣很容易就可以達到best case的情形。
- 所以提到alpha-beta Pruning的複雜度，我們通常就是講best case，也就是O(b^(m/2))，效率提升多少也有以下兩種說法：
    - Reduce the efficiency from branch factor b to square root b (如果tree的分支數是b 效率可提升到b開根號)
    - Make the search "twice" as deep as before. (如果tree的深度是m 效率可提升到m的兩倍深度)
- 能不能Pruing 更多？ 下面介紹兩種方法Asperasion Windows跟NegaScout

### Asperasion Windows
- Assume "Utility" is quite consistent.
- 如果棋盤盤面上的狀態都滿穩定的，對方下了兩步之後對盤面沒有太大影響，其實我們可以利用舊的盤面來預測接下來的盤面狀況
- 原本的[alpha, beta] = [-無限大, +無限大] 改成 [OLD_SCORE - 30, OLD_SCORE + 30] 來使pruning的條件更容易達成
- 要確保結果不失真，當你修改後的window有可能太小的情況下，你就必須要重新調整成更寬的window去re-search。
- 為什麼是＋-30這個也是一個可以tunning的參數（可以利用一些統計數值如平均值、第一四分位、第三四分位等資訊來調整）

### NegaScout
- Assume "Utility" is integral.
- 極端版的Asperasion Search.
- 如果不是1st move, 它會把window 從原本的[alpha, beta] 設成 [alpha, alpha+1]  (a null window). 讓cut off 非常容易發生
- If the assumption is correct, then search speed up.
- If the assumption is incorrect, then search it properly (just same as using the original [alpha, beta] window).

一個alpha-beta Pruning的例子：
![Imgur](http://i.imgur.com/IpFqBY7.png)

利用NegaScout後：
![Imgur](http://i.imgur.com/a6wnv0D.png)

- 上面的例子，最上面的節點下來，原本用[5, +無限大]改成用[5, 6], 進而讓[7, 6]那個節點位置發生pruning. 可是被剪掉的那個節點（7旁邊那個紅色圈圈）有可能不應該被剪掉的。繼續看下去
- 然後右邊那兩個都是4的節點還是都會跑到，但是兩個4在範圍[5, 6]而且Max的篩選下4比5小(Max修改alpha)，所以Max那個節點的更新還是維持在[5, 6]，但是會把4回傳上去。
- 4回傳上去後，會把原本範圍在[5, 6]而且Min的篩選下(Min修改beta)把範圍改成[5, 4]，（雖然這時發生cut off但旁邊也已經沒有別的分支了所以沒有pruning），然後繼續把4回傳上去。
- 接著對照最上面的節點[5, +無限大]. 4 在這個範圍之外，所以可以保證我們剛才pruning掉的那個節點（7旁邊那個紅色圈圈）是合法的。理由是：
    - 如果我們沒有用很極端的[5, 6]下去搜，而是用原本[5, +無限大] 下去搜。回傳可能的值一定小於等於4 ([5, +無限大]的搜尋範圍大，被prune掉的比較少，那找到的數字就會比較多，比較多的話也就只有4, 3 ,2 ,1 這些，也就是小於等於4)
    - 小於等於4 對最上面的Max節點來說沒有意義，prune掉的東西確定是安全的。
    - 不要用[5, +無限大]去想而是假定原本是[5, 10]的話, 如果你最後回傳20 (一樣在範圍之外)，那表示你也沒有多prune掉什麼東西，跟原本使用[5, 10]下去搜是一樣的意思。

### Imperfect Decisions, Forward Pruning
- 像在有些時間限制的競局下，我們必須用"Cutoff-Test"來取代"Terminal-Test", Cutoff-Test可以有以下方法：
    - Can be simple as depth limit. (但是會有horizon effet的問題, 搜到第八層就停，搞不好第九層扭轉局面)
    - Can adopt quiescence search to conquer the horizon effect. (當第五六七八層的heuristic震盪幅度很大時，表示目前狀況不穩定，有可能第九層扭轉盤面，那麼就不會搜到第八層停止，就繼續搜下去。)
    - Yet another good reason to adopt iterative deepening. Return the current best move when time's up. (但是一般用DFS的壞處是，時間到了而右半邊都還沒搜，有可能最好的解是在右半邊的。使用iterative deepening去解決，iterative deepening會優先走heuristic最好的那條)
- 但是heuristic只是預估值，一定會有不準的情形。我們既然要使用heuristic，那就代表我們容忍可以失真的結果。這就不像alpha-beta Pruning或MinMax Search保證完全不失真。如果可以容忍失真，那我們就pruning 更多應該也沒關係。
- Forward Pruning:
    - 使用Beam Search. 一開始keep k種選擇，剩下的全部prune掉。
    - 使用PROBCUT. 利用統計過往資訊計算這個節點可能被prune掉的機率，低於一個threshold就直接prune掉

### Search vs. Table Lookup
- For many games, deep search usually helps little at the beginning.
- Instead, fast table looking up, huge databases, and statistical analysis help more.
- Table lookup also helps a lot toward the end of games.
    - The KBNK (king, bishop, knight vs. king) lookup:
        - 462 * 62 * 61 * 2 = 3,494,568 possibilities
    - Bourzutschky (2006) solved all pawn-less 6-piece endgames and some 7-piece endgames.
    - A KQNKRBN endgame require 517 moves!
- Finally, early exchange favors computers than humans -> deeper search and more probable falls in lookup.
- Table Lookup通常在開局跟終局的時候經常使用。開局時很多盤面其實heristic都不會產生太多變化，通常走好幾步之後heristic才會有實際的作用；而終局雖然盤面上棋子很少了，跑Search演算法也會很快就跑完，但是用table loopup，對照目前盤面就直接查表去走完接下來的最佳解就好。

### Handling Repeated States
- We desire BACKTRACK implemetation instead of copying the whole board state.
- We desire to be able to identify repeated state very fast.
- We desire to store the scores of some recently-calculated states (transposition table).
- Zobrist Hashing:
    - Randomly generate Zobrist's keys (fixed) for each piece at each position. The hash key for a board state is then the XOR of these keys.
    - S (XOR) a (XOR) a = S 利用XOR這樣的特性，當原本的狀態 S做過一個action a之後跑到狀態S'. 要做backtrack, 把S'再XOR a 就可以回到原來的S.

### Stochastic Games
- 隨機的Game我們會加入Chance node去計算一個期望值。這邊又叫ExpectiMiniMax
- ExpectiMiniMax跟MinMax不同的是，定義heristic的「絕對值」也非常重要，不像MinMax只要確保相對大小就好了。這是因為ExpectiMiniMax還多了期望值的關係。
- Performance of ExpectiMiniMax:
    - alpha-beta pruning now does not apply to MAX/MIN nodes. (why?)
    - alpha-beta pruning applies to CHANCE nodes. (why?)
    - Time Complexity: O(b^m) -> O(b^m * n^m), where n is the number of distint dice rolls.
    - Cause ExpectiMiniMax impractical in many cases.
    - Solution: Instead of checking every MAX/MIN node, adopts "Monte Carlo Simulation" at CHANCE node.
    - Using random dice rolls to check only a certain number (decided by quality/time limit) of paths.
    - 蒙地卡羅：因為樹太龐大了，用ExpectiMiniMax可能只能走到第一二層這麼淺，蒙地卡羅的做法就是假設時間或限制條件允許你走一百萬條，那你就去看CHANCE node, 然後去擲骰子，機率夠大就去走，走一百萬條路徑。這樣可以確保你走得夠深。

### Partially Observable Games
- 不能觀察的部分是對手決定的，不是上面的機率隨機問題。譬如1~25賓果問題
- 所以什麼是optimal？這個部分也很arguable的，通常牽涉到賽局理論
- 賽局理論中，如果一個遊戲存在Nash平衡的話，我們通常說維持Nash平衡就是最佳解
- Nash Equilibrium: 如果每個玩家都知道其他玩家的策略下，還可以確定自己目前的策略是最好的並且堅持這個策略下去，就存在Nash平衡。譬如兩個人玩剪刀石頭布，就不存在Nash平衡。如果對方知道我出石頭，他一定會出布;那我知道他會出布的話，我就會出剪刀（就不會keep我原來出石頭的策略了）


Sum up
---
![Imgur](http://i.imgur.com/zF1mw1r.png)


Quiz
---

### Answer
1. (X) In a partially observable, turn-taking, zero-sum game between two perfectly rational players, it does not help the first player to know what move the second player will make, given the first player's move. (O) In a fully observable, turn-taking, zero-sum game between two perfectly rational players, it does not help the first player to know what strategy the second player is using, that is, what move the second player will make, given the first player's move.
2. ExpectiMiniMax
3. Yes, on chance node
4. (X) Yes, the first move player wins. (O) No, MiniMax may fail due to infinite loop search
5. Left-to-right: P; right-to-left: I, M, N
6. (X) L, O, P; No (X) N, H; Yes (O) L, O, P; Yes
7. Left
8. No; Yes
9. No
10. No
11. (X) Yes (O) No
12. (X) Yes (O) No
13. Yes
14. Yes


