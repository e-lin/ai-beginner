# Week6

Notes
---

### Logical Agent
- Logical就是定義formal language，然後目標並非如之前那樣在解決一個問題，而是利用這個定義出來的正規語言，去「描述」這個問題。當問題能夠被正規語言所描述出來，其實問題也就迎刃而解了。
- 邏輯的世界有：
    - "Logics" are formal language.
    - "Syntax" defines the sentence structures in the language.
    - "Semantics" defines the meaning of sentences.
        - Semantics定義句子裡面哪些為True. 我們又稱 possible world or true assignments.
        - 譬如 x+y = 4 is true in a world where x = 1 and y = 3.
    - 我們會用 "Model" 來表示 possible world (可以想像就是x+y=4那條線, 所有在那條線上的x,y都是possible world)

### Entailment and Models
- (x+y=4) entails (4=x+y)
- x=0 entails xy=0
- entails 跟 imply 乍看有點像。看下面的例子看差別在哪。

命題：
![Imgur](http://i.imgur.com/XL9vr0D.png)
「如果你考一百分，我就請你吃大餐。」

用implication來看這個命題，是True. 證明如下圖：
![Imgur](http://i.imgur.com/M3n7S6B.png)
「如果你考一百分，我就請你吃大餐。」如果是imply的話，意思就是「你沒有考一百分」-(a & b)跟「我請你吃大餐」(r)都會讓命題成立。「你沒有考一百分」的情況下因為我們沒辦法證明命題是假的，那只能相信如果我考一百分，你就會請我吃大餐為真。「我請你吃大餐」不管我有沒有考一百分，你都已經請我吃大餐了，所以為真。

用entailment來看這個命題，是Flase. 簡單講用entailment時最好不要做任何邏輯的運算。它的原則就是在「所有世界」裡面都要imply為真. 我們列出所有truth table來檢查：
![Imgur](http://i.imgur.com/0pbqGyQ.png)
(a & b)為真的情況下，r必須為真，所以對照table最下面兩行中，只有最後一行的世界是成立的。(你考了一百分，我一定要請吃大餐)。另外，當(a & b)為假的情況下，不管r為真或假，世界都算成立。(你沒有考一百分)

所以總共的世界有七種。記得原則就是在「所有世界」裡面都要imply為真. 我們先檢查七種世界底下，a 是否 imply r?
a imply r 只有六種世界：（星號部分）
![Imgur](http://i.imgur.com/FuOR3WQ.png)

所以七種世界裡面，a 並沒有全部 imply r。所以這個命題在entailment來看是Flase.

### Standard Logical Equivalences
![Imgur](http://i.imgur.com/5tebk2p.png)

### Validity and Satisfiability
![Imgur](http://i.imgur.com/bSHB5ex.png)

### Inference
![Imgur](http://i.imgur.com/DKmtXav.png)

- Soundness: 如果能證明出來的（inference），都對。
- Completeness: 對的（entailment），都能證明出來。

### Proof by Resolution
- 實際上要驗證entailment，列出truth table的方式不太可能一一對應在真實情況下，我們還是會用推導證明的方式。也就是利用上述那些logical equicalences, validity, satisfiability等等...但都還是需要人類的介入去選擇。要讓電腦自動去做推導，我們可以用 resolution 的方式進行。
要做 proof by resolution, 第一個就是要把你的sentence 轉換成 conjunctive normal form (CNF)
- CNF就是每個clause都是OR的結合，然後再把所有的clauses 做 AND結合起來。（轉換的時間運算上只要多項式複雜度而已 -> 可以想成接近線性的時間）
- 轉換成CNF後，可以利用「Resolution Inference Rule」把一些互補的clauses刪除掉 （Resolution Inference Rule可以用truth table證明）

### Resolution Algorithm
- 利用反證法導致矛盾。
![Imgur](http://i.imgur.com/caHHBNJ.png)
- 當KB也是一個CNF, 而(KB AND -a )整個也會是一個CNF
- Resolution Algorithm做的就是把兩兩clauses去做reslution. (Cn取2)
- 直到做完resulition不會再多出新的clause為止
- 最後如果是empty clause的話 回傳結果 True. 否則回傳False
- 而所謂empty clause就是 (a) AND (-a), (a) AND (-a)這樣的邏輯是不存在的，就意味著（KB AND -a ) is unsatisfiable. 即為 KB entails a.

### Resolution is Sound and Complete
- 要說明Resolution 是Sound的沒什麼好證明的
- 證明Resolution 是Complete需要：
    - 先知道什麼叫做 Resolution Closure RC(S) of a set of clauses: RC就是當一直做resolution不會再出現新的clasuse時的那些clause的集合
    - 知道什麼叫 Ground Resolution Theorem:
        - S is unsatisfiable => RC(S) contains the empty clause.
    - 這裡的S就是 （KB AND -a）, 所以要證明Resolution 是Complete等於去證明: 如果 （KB AND -a）是unsatisfiable, 則RC(S) 存在 empty clause, 那我們用反證法：如果RC(S) 沒有 empty clause, 那（KB AND -a）是 satisfiable的
    - 證明（KB AND -a）是 satisfiable的話，最簡單就是說明 KB = True, -a = True (True assignment) 那麼（KB AND -a）就會是True的
    - 我們給每個literals P1, ..., Pk 都為True. 但是如果一個clause裡面有 -Pi 跟其他literals都是False的話，那我們給 Pi為False
    - 再做一次反證，如果一個clause會是False的話，那不是 Pi 就是 -Pi 有一個為False, 可是依照我們的true assianment，不可能發生Pi 或 -Pi 有一個為False. 得證：（KB AND -a）是 satisfiable的
    - 而所謂Resolution is Complete 就是 只要 KB entails a, 我一定可以用resulution得到這個結果.

### Horn and Definite Clause
- 兩個Horn Clause 做完 disjunction 還是一個Horn Clause
- 其實Horn Clause可以看成 (-A OR -B OR -C OR D) 等於 -(A AND B AND C) OR D 就等於 A AND B AND C => D (imply)
- 雖然resolution是多項式複雜度，但如果我們是用 horn clause的話、再利用Forward 或Backward Chaining推導，可以降低為線性時間。
- Forward 跟Backward Chaining也都是Sound and Complete.

### Forward Chaining, Backward Chaining
![Imgur](http://i.imgur.com/TSyPXU9.png)


Sum up
---
![Imgur](http://i.imgur.com/ye1niWx.png)


Quiz
---

### Answer
1. (X) Soundness: Yes; Completeness: No (X) Soundness: Yes; Completeness: Yes (O) Soundness: No; Completeness: Yes
2. Soundness: Yes; Completeness: Yes
3. (X) p ⋀ q ⊨ p ⋁ q (O) ((p ⇒ q) ⋁ r) ⊨ (p ⇒ q)
4. (X) Soundness: Yes; Completeness: Yes (?) Soundness: Yes; Completeness: No
5. Every sentence can be converted to the conjunctive normal form.
6. (X) α ⊨ β iff M(α) ⊆ M(β) (X) m ∈ M(α) implies m is a model of α (O) Proposition can vary over time
7. These sentences can not be inferred from the current KB.
8. (X) The order of checking unknown symbols is wrong. (O) These sentences can not be inferred from the current KB


