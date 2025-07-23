#### Backtracking -> [Recursive-descent]([Recursive descent parser with simple backtracking](https://gist.github.com/DmitrySoshnikov/e2c3a793636dc03f2200))

```js
/**
 * 
 *   E -> T         E1 - Product 01
 *      | T + E     E2 - Product 02
 *      ;
 * 
 *   T -> a            - Product 03
 *      | a * T        - Product 04
 *      | (E)          - Product 05
 *      ;
 */
 
 function term(a) {
     return GetNextToken() == a;
 }
 
 function GetNextToken() {
     while (source[cursor] === ' ') cursor++;
     var nextToken = source[cursor];
     cursor++;
     return nextToken;
 }
 
 var savedCursor;
 
 function saveCursor() {
     savedCursor = cursor;
 }
 
 function backtrack() {
     cursor = savedCursor;
 }
 
 function E() {
     return    (saveCursor(), E1()) 
            || (backtrack(), saveCursor(), E2());
 }
 
 function E1() {
     return T();
 }
 
 function E2() {
     return T() && term('+') && E();
 }

 function T() {
     return    (saveCursor(), T2()) 
           || (backtrack(), saveCursor(), T3()) 
           || (backtrack(), saveCursor(), T1());
 }
 
 function T1() {
     return term('a');
 }
 
 function T2() {
     return term('a') && term('*') && T(); 
 }
 
 function T3() {
     return term('(') && E() && term(')'); 
 }
 
var source;
var cursor;

function parse(s) {
    source = s;
    cursor = 0;
    return E() && cursor == source.length;
}

console.log('(a)', parse('(a)'));

console.log('-a', parse('-a')); 

console.log('a * a', parse('a * a'));

```

## Left Elimination :

#### Converting Backtracking to Predictive:

```
E -> T E';
 
E' -> + E
   | e
   ;
   
T -> a T'
   | (E)
   ;
   
T' -> * T
   | e
   ;
```

## Code:

```Js

 function term(expected) {

     return GetNextToken() === expected;

 }

 function GetNextToken() {

     while(source[count] == ' ') count++;

     return source[count];

 }

 function E() {

     return T() && _E();

 }

 function _E() {

     if(term('+')) { // Concept of lookahead tokens

         count++;

         return E();

     }

     return true;

 }

 function T() {

     if(term('a')) { 

         count++;

         return _T();

     }

     if(term('(')) {

        count++;

        if(!E() || !term(')')) return false;

        return count++;

     }

     return false;

 }

 function _T() {

     if(term('*')) {

         count++;

         return T();

     }

     return true;

 }

 var count;

 var source;

 function parse(s) {

     source = s;

     count = 0;

     return E() && count === source.length;

 }
/*
console.log("a * a : " + parse("a * a"))

console.log("a + a * a : " + parse("a + a * a"))

console.log("a + a * a + a : " + parse("a + a * a + a"))

console.log("(a * a + a) : " + parse("(a * a  + a)"))

console.log("(a * (a * a)) : " + parse("(a * (a * a))"))

console.log("(a * a + a) + (a * a) : " + parse("(a * a + a) + (a * a)"))

console.log("(a * a + a) * (a * a) : " + parse("(a * a + a) * (a * a)"))

console.log("(a * a + a) + a : " + parse("(a * a + a) + a"))

console.log("(a * a + a) * a : " + parse("(a * a + a) * a"))

console.log("a + (a + a) * a : " + parse("a + (a + a) + a"))
*/
```


### LL(1)
```js
// Map of Non-Terminals (NT) and Tokens to Production Rules

// Example grammar:

// E  -> T _E

// _E -> + E | ε

// T  -> F _T

// _T -> * T | ε

// F  -> ( E ) | num

  
  
  

const productionMap = {

  'E': {

    'LPAREN': ['_E', 'T'],

    'NUMBER': ['_E', 'T']

  },

  '_E': {

    'PLUS': ['_E', 'T', 'PLUS'],

    'RPAREN': [' '],      // epsilon

    'EOF': [' ']    

  },

  'T': {

    'LPAREN': ['_T', 'F'],

    'NUMBER': ['_T', 'F']

  },

  '_T': {

    'MUL': ['_T', 'F', 'MUL'],

    'PLUS': [' '],    

    'RPAREN': [' '],    

    'EOF': [' ']    

  },

  'F': {

    'LPAREN': ['RPAREN', 'E', 'LPAREN'],

    'NUMBER': ['NUMBER']

  }

};

  
  

const NonTerminalSet = ['E', '_E', 'T', '_T', 'F'];

  

function isNonTerminal(value) {

  return NonTerminalSet.indexOf(value) != -1;

}

  

function tokenize(input) {

  

  const tokens = [];

  const tokenSpec = [

    { regex: /^\s+/, type: null }, // Skip whitespace

    { regex: /^\d+/, type: 'NUMBER' },

    { regex: /^\+/, type: 'PLUS' },

    { regex: /^\*/, type: 'MUL' },

    { regex: /^\(/, type: 'LPAREN' },

    { regex: /^\)/, type: 'RPAREN' },

  ];

  

  while (input.length > 0) {

    let matched = false;

  

    for (const { regex, type } of tokenSpec) {

      const match = input.match(regex);

      if (match) {

        matched = true;

        if (type) {

          tokens.push(type);

        }

        input = input.slice(match[0].length);

        break;

      }

    }

  

    if (!matched) {

      throw new Error('Unexpected token: ' + input[0]);

    }

  }

  

  tokens.push('EOF');

  

  return tokens;

}

  

 function parse(s) {

  

    var source = tokenize(s);

  

    var count = 0;  

  

    var stack = [];

  

    stack.push('EOF');

  

    stack.push('E');

    while(true) {

  

    var top = stack.at(stack.length - 1);;

  

    var  lookahead = source[count];

  

     if(top == 'EOF' && lookahead == 'EOF') break;

  

     if(lookahead == 'EOF') {

      if(productionMap[top]['EOF'] == null) return false;

       stack.pop();

       continue;

      }

  

     if(isNonTerminal(top)) {

  

      var rule = productionMap[top][lookahead];

       if(rule == null) return false;

  

      if (rule.length === 1 && rule[0] === ' ') {

        stack.pop(); // epsilon — do nothing, just pop

      }  

       else {

        stack.pop();

         for(var i = 0; i < rule.length; i++) {

          stack.push(rule[i]);

          }

        }

       continue;

      }

  

     if(lookahead !== top) return false;

     stack.pop();

     count++;  

    }

  

    return true;

}

  

console.log("7: " + parse("7"));
console.log("7 + 3: " + parse("7 + 3"));
console.log("4 * 5: " + parse("4 * 5"));
console.log("2 + 3 * 4: " + parse("2 + 3 * 4"));
console.log("( 7 ): " + parse("( 7 )"));
console.log("( 2 + 3 ) * 5: " + parse("( 2 + 3 ) * 5"));
```