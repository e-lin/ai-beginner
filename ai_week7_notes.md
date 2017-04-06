# Week7

Notes
---

- Propositional Logic的缺點是表達不好，而用Fisrt-Order Logic的話幾乎可以完整表達所有的事情。
- First-Order Logic:
    - Syntax and semantics
    - 學會怎麼用FOL表達事情
- 兩種Inference
    - Instantiation (then using Propositionalization)
    - Unification (then using Forward/Backward chaining or Resolution)

- Instantiation 再Propositionalization的時間複雜度在多項式，n^1, n^2, n^3 都還可以接受，但是當n^k 這個k超過3其實就很沒效率了。


Sum up
---
![Imgur](http://i.imgur.com/nuTYVad.png)


Reference
---
- [Inference in first-order logic Outline Universal instantiation (UI ...][R1]
- [First Order Logic: Conversion to CNF][R2]


Quiz
---

### Answer
1.　∀x Likes(x,IceCream) ⇒ ∃x ¬Likes(x,IceCream)
2. Eavesdrop(Alice, y, z) ∧ Talk(y, z) ⇒ Sniffer(Alice) / Eavesdrop(Bob, y, z) ∧ Talk(y, z) ⇒ Sniffer(Bob) / Eavesdrop(Oscar, y, z) ∧ Talk(y, z) ⇒ Sniffer(Oscar)
3. Eavesdrop(x, C1, C2) ∧ Talk(C1, C2) ⇒ Sniffer(x)
4. After UI: Yes; after UI and EI: No
5. Forward: sound and complete; Backward: sound but incomplete
6. Due to memory restriction and no occur check
7. (O) (∀x ∀y P(x, y)) ≣ (∀y ∀x P(x, y)) ; (∃x ∀y P(x, y)) ⊨ (∀y ∃x P(x,y)) ; (∀x ∀y P(x, y)) ⊨ (∀y ∀x P(x, y)) ; (∃x ∃y P(x, y)) ⊨ (∃y ∃x P(x,y))
8. [ ¬Coding(x) ⋁ ¬AskHelp(x, y) ] ; [ Welcomed(x) ⋁ AskHelp(x, F(x)) ]
9. (X) The forward chaining will infer the result SpeakChinese(Su) ; If the backward chaining does not return True for a given query, then it is not entailed by the KB ; Given the query SpeakChinese(Su), The backward chaining will loop forever


[R1]: https://www.cs.sfu.ca/~mori/courses/cmpt310/slides/chapter09-6pp.pdf
[R2]: https://april.eecs.umich.edu/courses/eecs492_w10/wiki/images/6/6b/CNF_conversion.pdf
