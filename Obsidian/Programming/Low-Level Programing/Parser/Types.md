
# Jison or Bison/Yacc-style
### Top-down parsers

- Backtracking  ||  Predictive
	- [ Recursive-descent]([Recursive descent parser with simple backtracking](https://gist.github.com/DmitrySoshnikov/e2c3a793636dc03f2200))
	- Non-recursive LL(1)

#### Left recursion is bad for Top-down parsers.
**Example of left recursion:**
```q
E → E + T | T
```

> This causes the parser to keep calling `E` again and again without consuming input.

### ✅ **Eliminating left recursion** is a key step in moving from **backtracking** to **predictive parsing**.

### 🛠️ Fixing Left Recursion:

- Use **left recursion elimination**, not left factoring.
    
- Example:
```q
Left-recursive: E → E + T 
					| T
					;
					
Eliminated: E  → T E';

			E' → + T E'                              // OR E' -> + E
				| ε
				;

Left-recursive: T -> a
			    | a * T
			    | (E)
			    ;

Eliminated: T  → a T'
			    | (E)
			    ;
			T' → * T 
				| ε
				;
```

| Concept                        | Purpose                                                               | Fixes What?                    | Example                                |
| ------------------------------ | --------------------------------------------------------------------- | ------------------------------ | -------------------------------------- |
| **Left Recursion Elimination** | Avoids infinite recursion in top-down parsers                         | Left-recursive grammar rules   | `E → E + T` → `E → T E'`, `E' → + T E' |
| **Left Factoring**             | Helps predictive parsers choose the correct rule without backtracking | Rules with **common prefixes** | Below                                  |
**Left Factoring:**
```q
S → E
  | E 'a'
	
S → E S'
S' → 'a' | ε
```

#### Example:
```q
Left Recursion => yxxxxx
A : A x
  | y
  ;

Right Recursion (Not Valid Elimination) => xyyyyy
A : x A
  | y
  ;

Left Recursion Elimination
A : y A'
  ;
  
A': x A'
  | ε
  ;
```

#### Indirect Left Recursion:
```q
A → B x  
B → C y  
C → A z

=> C -> C y x z (Direct Left Recursion)

C -> yxz C | yxz (?)
```


##### An **LL(1) parser** examines **just one token ahead** (1 lookahead) at each step to decide which production rule to apply. using parsing table. (BNF + F&F Sets)

**L - Scan From Left To Right
L - Using Left-most derivation
1 - Using 1 lookahead token**

### First & Follow Sets (automated non-recursive parser)

#### [First] - Set of tokens from which a non-terminal can **start** in a string
- term(x)                then First(S) = x
- X -> ε                  then First(X) = ε
- if X -> Y1Y2Y3     then First(X)  = First(Y1, Y2, Y3)


#### [Follow] - Set of tokens which can **follow** a non-terminal in a string

- Put $ to Follow(Starting-Non-Terminal)
- A -> aB     then Follow(B) = Follow(A)
- A -> aBC   | if First(C) == ε then Follow(B) = Follow(A)

>ε - not allowed

> X : a B C D
	follow(D) = follow(X) -- not vice-versa
	follow(B) = first(CD)
   ;


### Examples :

```q
S : a B C ;
B : b | ε ;
C : c     ;
```

Follow(S) = $
Follow(B) = First(C)
Follow(C) = Follow(S)

| Non-Terminal | First Set | Follow Set |
| ------------ | --------- | ---------- |
| S            | a         | $          |
| B            | ε, b      | c          |
| C            | c         | $          |

> $ - end of the string



```q
E -> T E';        - production 01
 
E' -> + T E'      - production 02
   | ε            - production 03
   ; 
   
T -> F T'         - production 04
   ;
   
T' -> * F T'      - production 05
   | ε            - production 06
   ;
   
F -> number       - production 07
   | (E)          - production 08
   ;
```

> X : a B C D
	follow(D) = follow(X)
	follow(B) = first(CD)
   ;


First(E) = First(T) = First(F)

Follow(E') = Follow(E)

Follow(T) = First(E') {
since ε -> E' -> + T
	Follow(T) = Follow(E')
}

Follow(T') = Follow(T)

Follow(F) = First(T') {
since ε -> T' -> * F
	Follow(F) = Follow(T')
} 

| Non-Terminal | First Set | Follow Set |
| ------------ | --------- | ---------- |
| E            | (, number | $, )       |
| E'           | ε, +      | $, )       |
| T            | (, number | + ,$, )    |
| T'           | ε, *      | + ,$, )    |
| F            | (, number | + ,$, ),*  |

| Non-Terminal | First Set | Follow Set |
| ------------ | --------- | ---------- |
| E            |           | $, +       |
| T            |           | $, +, \*   |
| F            |           | $, +, \*   |
|              |           |            |
|              |           |            |
### Parsing Table

E : ( , number

E': ε, +
	since ε => Follow(E') = $, )
	
T : ( , number

T': ε, *
	since ε => Follow(T') = + ,$, )
	
F : ( , number

| Non-Terminal | +   | *   | (   | )   | Number | $   |
| ------------ | --- | --- | --- | --- | ------ | --- |
| E            |     |     | 1   |     | 1      |     |
| E'           | 2   |     |     | 3   |        | 3   |
| T            |     |     | 4   |     | 4      |     |
| T'           | 6   | 5   |     | 6   |        | 6   |
| F            |     |     | 8   |     | 7      |     |

>Predict Set = (epslon ? First Set  + Follow Set : First Set)


Predict Set is a map :

	E': +: 2, $: 3, ): 3 =              E': +, ε     +       E': $, )


#### LL1 Conflicts:
- First/First
	Happens because of No Left Factoring

- First/Follow
```q
S: A 'a' 'b'     -p1
 ;
A: 'a'           -p2
 | ε             -p3
 ;
```

| Non Terminal |   a   | 
| ------------ | ----- |
| S            |   1   |
| A            |  2/3  |

- Follow/Follow
	- Theroical, only happen if  2 path for epselon


Parsing Stack :

Input string : "'(5+3) * 2"
rule#1 : if there is non terminal on top of the stack replace it with RHS of the
T[NT, lookahed] = production
### Bottom-up parsers (aka. Shift-Reduce)

- LR(0)
- SLR(1)
- CLR(1)
- LALR(1)


Canonical Collection Of LR-items
- LR-Item -- a **production** with a **dot-cursor**

### 🔹 LR(0) Items for `S → X X`

There are **three LR(0) items**:

1. `S → • X X` — nothing has been parsed
    
2. `S → X • X` — one `X` has been parsed
    
3. `S → X X •` — the whole rule has been parsed

---

```q
A : B A B | B
B : 1 C C
C : 0 | ε 
```

**Augmented** CFG (Context Free Grammar):

```q
A' : A   // augmented production
A  : B A B
A  : B
B  : 1 C C
C  : 0
C  : ε
```

🔹**Closure** operation :

The first item set we build — typically called **I₀** — is the result of taking:

```q
A' → • A
```

...and then **recursively expanding** every item where the dot is before a non-terminal.

```q
A' : • A
A  : • B A B
A  : • B
B  : • 1 C C
```

1. `A' → A • ` — Indicate the program has finished


🔹**Go-to** operation

> When an item has a **dot before a non-terminal**, we **add all productions** of that non-terminal to the item set.

**GOTO(`I₀, A`)**
	``I₁ = A' : A •``  <=This item is the **Kernel** that cause the transition to this state, aka Basis or "core", it can be one or many.

**GOTO(`I₀, B`)**
	``I₂ = A' : B • A B``
	    ``A  : B •    ``
	    ``A  : • B A B``
	    ``A  : • B    ``	
	    ``B  : • 1 C C``
	
**GOTO(`I₀, 1`)**
	``I₃ = B : 1 • C C``
	    `` C  : • 0  ``
	    `` C  : • ε  ``

....... [More](https://www.youtube.com/watch?v=BxMFn7aelBk&list=PLxP0p--aBHmJKqrSFBatFypgviqGRxMKO&index=23)


🔹**LR Parsing** Table
[Reference](https://www.youtube.com/watch?v=YjEck_rmrQs&list=PLxP0p--aBHmJKqrSFBatFypgviqGRxMKO&index=23)   -> if reduced it also | s1/r4 | which will cause shift/reduce conflict (not LR0 Grammar)

🔹**LR - SLR - CLR - LALR Parsing** Algorithm
[Reference](www.youtube.com/watch?v=zIQgky-hXJ4&list=PLxP0p--aBHmJKqrSFBatFypgviqGRxMKO)

🔹**LR** Conflicts
- Shift / Reduce
- Reduce / Reduce
> the parser doesn't know **what action to take** for a given input and state.

🔹Shift is pushing to the stack
🔹Reduce is pop from the stack and replace with the left non-terminal


SLR - **simple LR parser** - and LR(0) - **basic LR parser** - are the same but different only in the reduce section, but they are rarely used in context-free grammar - CLR and LALR are preferred -

CLR and LALR use [LR(1) Automaton.](https://www.youtube.com/watch?v=sh_X56otRdU&list=PLxP0p--aBHmJKqrSFBatFypgviqGRxMKO)

**SLR, LALR, and CLR parsers share the same core LR algorithm logic**—they all use **shift-reduce parsing** with states derived from a grammar's items. However, their parsing tables differ due to **how they handle lookaheads** and **state merging**.


### 🔹 CLR(1) and LALR(1)

CLR canonical LR parser
LALR Lookahead LR parser
LR(1) = LR(0) + lookahead











LR0     : **small** number of states, **worst reduce**
SLR1   : **small** number of states, **better reduce**

CLR1   : **much more** number of states, **best reduce**
LALR1 : **small** number of states, **good reduce**