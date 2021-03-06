# Leesp  
A Lisp-like implementation written in C with some high-level portions bootstrapped in Leesp.

# To Compile
On Linux and Mac, simply use `make` to execute the recipe in the makefile, or
```
cc -std=c99 -Wall include/mpc/mpc.c main.c -ledit -lm -o leesp
```
On Windows
```
cc -std=c99 -Wall include/mpc/mpc.c main.c -o leesp
```

# Arithmetic operators
Leesp uses Polish Notation (prefix notation) for mathematical sequences. 
```
leesp> + 5 3
8

leesp> - 8 2
6

leesp> * 3 5
15

leesp> / 8 4
2

leesp> + 4 (* 3 2) (- 9 (/ 12 2))
13
```

# Comparison operators
Leesp supports the standard comparison operators of `>`, `<`, `>=`, `<=`, `==`, and `!=`. This will also be in polish notation, and comparison returns `1` when `true` and `0` when `false`.
```
leesp> > 1 2
0
leesp> < 1 2
1
leesp> == 1 2
0
leesp> != 1 2
1
leesp> <= 2 2
1
```

# Logical operators
`not`
```
leesp> not true
0
leesp> not false
1
```
`or`
```
leesp> or true false
1
leesp> or false false
0
```
`and`
```
leesp> and true false
0
leesp> and true true
1
```

# S-Expressions
Lisp-like symbolic expressions. These are simply one or more expressions inside parentheses.
```
leesp> (+ 2 2)
4
```

# Q-Expressions
Quoted expressions to mimic some functionality of Lisp macros. When evaluated, they are left as is and not handled by normal mechanisms. This allows code to be written, and be passed as data itself.
```
leesp> {+ 2 2}
{+ 2 2}
```

# Strings
Strings are defined by using double quotes only.
```
leesp> "hello, world"
"hello, world"
```

# Built in functions
Leesp has multiple built in functions that are always available, listed below in alphabetical order.

## eval
Takes a Q-Expressions and evaluates it as a S-Expression
```
leesp> eval {+ 1 2}
3
```

## def
Define a variable
```
leesp> def {a} 2
()
leesp> a
2
leesp> def {b c} 3 4
()
leesp> + a b c 1
10
```

## head
Takes a Q-Expression and returns a new Q-Expression containing only the first element
```
leesp> head {8 10 12}
{8}
```

## join
Takes one or more Q-Expressions and returns a Q-Expressions of them joined together
```
leesp> join {1 2} {3 4}
{1 2 3 4}
```

## list
Takes one ore more arguments and returns a new Q-Expression containing the arguments
```
leesp> list 1 2 3
{1 2 3}
```

## tail
Takes a Q-Expression and returns a new Q-Expression with the first element removed
```
leesp> tail {7 8 9}
{8 9}
```

## len
Returns the length of a Q-Expression.
```
leesp> len {1 2 3 4 5}
5
```

## fst
Returns the first element from a Q-Expression
```
leesp> fst {1 2}
1
```

## map
Applies to a function to each element in a Q-Expression, returning a new Q-Expression with the results
```
; multiply all values by 2
leesp> map (\ {x} {* x 2}) {1 2 3 4 5}
{2 4 6 8 10}
```

## filter
Takes a Q-Expression and a functional condition, and returns a new Q-Expression containing only the elements that match this condition
```
; return all integers greater than 0
leesp> filter (\ {x} {> x 0}) {5 8 -3 4 -12 0}
{5 8 4}
```

## sum
Returns the sum of all elements in a Q-Expression
```
leesp> sum {2 4 8}
14
```

## product
Returns the product of all elements in a Q-Expression
```
leesp> product {2 4 8}
64
```

## unpack (also aliased as curry)
Takes a function and a Q-Expression of arguments, and calls the provided function with those arguments.
```
leesp> unpack + {1 2 3}
6
```

## pack (also aliased as uncurry)
Takes a function and a variable list of arguments, and calls the provided function with a Q-Expression of those arguments.
```
leesp> pack head 1 2 3
1
```

# User defined functions
The `func` keyword is used to define new functions. The first argument is a Q-Expression containing a name followed by any number of function arguments. The second argument is the function defintion.
```
leesp> func {multiply x y} {* x y}
()
leesp> multiply 2 4
8
```
Leesp functions support partial evaluation by providing less than all of the arguments.
```
leesp> def {double} (multiply 2)
()
leesp> double 8
16
```

# Control flow
Leesp supports `if` statements for basic control flow. Note, Leesp follows the C standard that all non-zero integers are truthy, so negative integers are still truthy. Only zero evaluates to false.
```
leesp> def {x y} 10 20
()
leesp> func {square_largest x y} {if (> x y) {* x x} {* y y}}
()
leesp> square_largest 10 20
400
```

`select` statements are also supported.
```
(func {month_day_suffix i} {
  select
    {(== i 1)  "st"}
    {(== i 2)  "nd"}
    {(== i 3)  "rd"}
    {otherwise "th"}
})

month_day_suffix 1
; "st"
month_day_suffix 2
; "nd"
month_day_suffix 3
; "rd"
month_day_suffix 8
; "th"
```

# Recursion
`if` statements allow the use of recursive functions due to conditional checking of a base case. Below is an example of creating a function `len` to determine the length of a list:
```
leesp> func {len x} {if (== x {}) {0} {+ 1 (len (tail x))}}
()
leesp> len {0 1 2 3 4}
5
```

# Comments
As in Lisp, comments are defined by using `;` and run to the end of the line.
