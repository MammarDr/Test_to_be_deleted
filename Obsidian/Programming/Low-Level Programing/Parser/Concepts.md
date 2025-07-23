
### 🧠 What is a Semantic Action?

A **semantic action** is a piece of code (usually written in C/C++, Java, etc.) that runs **when a grammar rule matches** during parsing.

It tells the parser:

> "When you successfully match this rule, here's what to do with it (e.g., build a node, evaluate a value, etc.)."

---

### ✅ Example Breakdown

```bnf
NumericalLiteral  
: NUMBER { $$ = NUMBER($1) }
;
```



#### 🔍 Breakdown:

- `NumericalLiteral` → the **non-terminal** you're defining
    
- `NUMBER` → the **token** you're matching (like `42`, `3.14`)
    
- `{ $$ = NUMBER($1); }` → the **semantic action**
    

---

### 💡 What the Semantic Action Means

| Symbol       | Meaning                                                                                  |
| ------------ | ---------------------------------------------------------------------------------------- |
| `$1`         | Refers to the value of the **first item** on the right side (here: `NUMBER`)             |
| `$$`         | The **result value** of the entire rule (assigned to `NumericalLiteral`)                 |
| `NUMBER($1)` | Some custom logic or function wrapping the value (e.g., creating an AST node or casting) |

So this action says:

> "When you see a `NUMBER`, wrap it or convert it using `NUMBER(...)`, and that becomes the value of `NumericalLiteral`."


### Format #1

```Bison
// ------------------------------
// Lexical Grammar

%lex

%%

\s+		/**/

\d+ 		return 'NUMBER'

/lex

// ------------------------------
// Snytatic Grammar

%%

Expression
	: AdditiveExpression
	;

AdditiveExpression
	: AdditiveExpression '+' MultiplicativeExpression { $$ = $1 + $3 }
	| AdditiveExpression '-' MultiplicativeExpression { $$ = $1 - $3 }
	| MultiplicativeExpression
	;

MultiplicativeExpression
	: MultiplicativeExpression '*' PrimaryExpression { $$ = $1 * $3 }
	| MultiplicativeExpression '/' PrimaryExpression { $$ = $1 / $3 }
	| PrimaryExpression
	;

PrimaryExpression
	: Literal
	| ParenthesisExpression
	;

Literal
	: NumericalExpression
	;

NumericalExpression
	: NUMBER { $$ = Number($1) }
	;

ParenthesisExpression
	: '(' Expression ')' { $$ = $2 }
	;

```

### Format #2

```
// ------------------------------
// Lexical Grammar

%lex

%%

\s+		/**/

\d+ 		return 'NUMBER'

[+\-]		return 'ADDITIVE_OPERATOR'

[*/]		return 'MULTIPLICATIVE_OPERATOR'

/lex

// ------------------------------
// Snytatic Grammar

%%

Expression
	: AdditiveExpression
	;

AdditiveExpression
	: AdditiveExpression ADDITIVE_OPERATOR MultiplicativeExpression 
	     {
	       $$ = {
		  Type  : 'BinaryExpression',
		  op    : $2,
	          left  : $1,
	          right : $3,
	       }
	     }	
		
	| MultiplicativeExpression
	;

MultiplicativeExpression
	: MultiplicativeExpression MULTIPLICATIVE_OPERATOR PrimaryExpression 
	     {
	       $$ = {
		  Type  : 'BinaryExpression',
		  op    : $2,
	          left  : $1,
	          right : $3,
	       }
	     }	
	| PrimaryExpression
	;

PrimaryExpression
	: Literal
	| ParenthesisExpression
	;

Literal
	: NumericalLiteral
	;

NumericalLiteral
	: NUMBER 
	{
	 $$ = { 
	  Type: 'NumericalLiteral',
	  value: Number($1),
	 }
	}
	;

ParenthesisExpression
	: '(' Expression ')' 
	{
	 $$ = { 
	  Type: 'ParenthesisExpression',
	  value: Number($2),
	 }
	}
	;

```


```
%left '+'
%left '*'

%%

E : E '+' E | E '*' E | NUMBER;
```


``

```
// ------------------------------
// Module Include

%{
function BinaryExpression(left, op, right) {
	return {
		  Type  : 'BinaryExpression',
		  op    : op,
	          left  : left,
	          right : right,
	       };
}

%}

// ------------------------------
// Snytatic Grammar

%%

Expression
	: AdditiveExpression
	;

AdditiveExpression
	: AdditiveExpression ADDITIVE_OPERATOR MultiplicativeExpression 
	     { $$ = BinaryExpression($1, $2, $3); }	
	| MultiplicativeExpression
	;

MultiplicativeExpression
	: MultiplicativeExpression MULTIPLICATIVE_OPERATOR PrimaryExpression 
               { $$ = BinaryExpression($1, $2, $3); }               
	| PrimaryExpression
	;

.......

```

### Commands:

```
syntax-cli --grammar letter.bnf --mode LALR1 --parse '2+2'

syntax-cli --grammar letter.bnf --mode LALR1 --file file.lt

syntax-cli --grammar letter.bnf --mode LALR1 --validate

syntax-cli --grammar letter.bnf --mode LALR1 --table 

syntax-cli --grammar letter.bnf --mode LL1 --table   // Parsing Table

syntax-cli --grammar letter.bnf --mode LL1 --sts first,follow

syntax-cli --grammar LR/lr0.bnf --collection --mode LR0 

syntax-cli --grammar letter.bnf --mode LR0 --table   // Parsing Table

syntax-cli --grammar letter.bnf --mode CLR1 --collection --table   // Collection & Parsing Table
```


> syntax-cli --grammar letter.bnf --mode LALR1 --validate

Spot the production causing the error
### ⚙️ Real Tool Examples

|Tool|Language|Purpose|
|---|---|---|
|ANTLR|Java/C#/Python|Powerful parser generator|
|Bison/Yacc|C/C++|Classic parser generators|
|Jison|JavaScript|For building JS parsers (like what you're using!)|
|PEG.js|JavaScript|Parsing expression grammar|
|syntax-cli|JavaScript|Minimal BNF parser (your current tool)|
