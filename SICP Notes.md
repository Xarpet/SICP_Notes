[toc]

# Preface: Resources

- [Companion website by MIT](https://mitpress.mit.edu/sites/default/files/sicp/index.html)
- [中文版题解](https://sicp.readthedocs.io/en/latest/)
- [SICP-solutions](http://community.schemewiki.org/?SICP-Solutions)

# Chapter 1: Building Abstractions with Procedures

Through out the book we will use `Scheme`, a dialect of lisp to program.

## 1.1 The Elements of Programming

> A powerful programming language is more than just a means for instructing a computer to perform tasks. e language also serves as a framework within which we organize our ideas about processes.

Every powerful programming language has three main ways of combining simple ideas to form more complex ideas:

1. **Primitive Expressions**
2. **Means of Combination**
3. **Means of Abstraction**

### 1.1.1 Expressions

Scheme uses prefix notation and **parenthesis** to form combinations: Operators first, operands follow:

```scheme
(+ 137 349)
:486
(- 1000 334)
:666
(+ 1 2 3)
:6
(* 0 3 7)
:0
```

You can also nest expressions. The standard way of doing this is called *pretty-printing*:

```scheme
(+ (* 3
      (+ (* 2 4)
         (+ 3 5)))
   (+ (- 10 7)
      6))
```

You work from the right most, all the way to the left most. Note that in Scheme you don't need to explicitly `print` expressions to get the values from the compiler.

> Lisp programmers know the value of everything but the cost of nothing.

### 1.1.2 Naming and the Environment

We name variables with `define`. This is the simplest way of abstraction.

```scheme
(define size 2)

size
:2
```

The `environment` is where the variables are stored (in this case the `global environment`).

### 1.1.3 Evaluating combinations

The rule of evaluating combinations:

1. Evaluate the subexpressions of the combination
2. Apply the value of the operator to the operands.

Note that the first rule implies that we must also evaluate the operator since it might also be a compound combination.

After repeated evaluating, we will come to a point where all the members of the combinations are all primitive objects, which can be then dealt with using:

- The values of numerals are the numbers that they name
- The values of built-in operators are the *machine instruction* sequences that carry out the corresponding operations
- The values of other names are the objects associated with those names in the environment

We may regard the second rule as a special case of the third, when we regard symbols like `+` and `*` are included in the global environment and has the corresponding machine instructions as their values.

Notice that definitions are not combinations. The parenthesis form of definition process is just a *special form* (syntactic sugar) in lisp to provide convenience. Lisp has a very simple syntax.

### 1.1.4 Compound Procedures

In [1.1.2](#1.1.2-naming-and-the-environment) we learned how associating names with values provide a limited means of abstraction. Now we will learn about `procedure definitions`, where a compound operation can be given a name and referred to as a unit. (That's just a fancy way of sayin function lol)

```scheme
(define (square x) (* x x))
```

The `x` after square is a *formal parameter*. The general form of compound procedures is:

```scheme
(define (<name> <formal parameters>)
  		<body>)
```

The `<body>` is an expression that will yield the value of the procedure. More generally, the body can be a sequence of expressions where the only the last value will be pushed.

You can also use procedures to define other procedures, like:

```scheme
(define (sum-of-squares x y)
  (+ (square x) (square y)))

(sum-of-squares 3 4)
:25
```

### 1.1.5 The Substitution Model for Procedure Application

If a combination has an operator that is a compound procedure, the intepreter must follow a certain sequence of process to evaluate the operators and the operands. More specifically, the formal parameters in the body of the procedure (operator) needs to be substituted by the operands(whether already evaluated to primitive objects or still remains to be expressions. See below.)

The most intuitive and what lisp uses is *applicative-order evaluation*, where all the operands are fully evaluated to primitive objects before substituting the formal parameters in the procedure. 

```scheme
(sum-of-squares (+ 2 1) (- 5 1))
(sum-of-squares 3 4)
(+ (square 3) (square 4))
(+ (* 3 3) (* 4 4))
(+ 9 16)
25 ;applicative-order evaluation
```

Another type of evaluation strategy is called *normal-order evaluation* (there are more) where you do not evaluate any of the arguments until they are needed in the body of the function (or obtained an expression involving only primitive operators).

```scheme
(sum-of-squares (+ 2 1) (- 5 1))
(+ (square (+ 2 1)) (square (- 5 1)))
(+ (* (+ 2 1) (+ 2 1)) (* (- 5 1) (- 5 1)))
(+ (* 3 3) (* 4 4))
(+ 9 16)
25 ;normal-order evaluation
```



It can be proved that, for legitimate procedures, two strategies will yield the same result.

### 1.1.6 Conditional Expressions and Predicates

We can perform a `case analysis` using `cond`.

```scheme
(define (abs x)
	(cond ((> x 0) x)
		  ((= x 0) 0)
		  ((< x 0) (- x)))) ; note that the `-` operator negates its only parameter

```

The general form of a conditional expression is:

```scheme
(cond (<predicate1> <expression1>)
      (<predicate2> <expression2>)
      ...
      (<predicaten> <expressionn>))
```

If one of the predicate is evaluated to be true, the intepreter returns the value of the corresponding expression and stop evaluating predicates.
Predicates are procedures that return true or false.

You can also rewrite the `abs` as:

```scheme
(define (abs x)
  (cond ((< x 0) (-x))
        (else x)))
```

Here `else` is basically just `always true`. In fact you can use any always-true-expression as the final predicate here.
There's another way using `if`

```scheme
(define (abs x)
  (if (< x 0)
      (- x)
      x))
```

The general form of `if` is:

```scheme
(if <predicate> <consequent> <alternative>)
```

Note that a minor difference between `cond` and `if` is that in `cond` the expression can be a sequence of expressions and the last expression will be returned. However in `if` it must be single expressions.

 In addition to primitive predicates such as `<`, `+` and `>`, there are logical composition operations:

```scheme
(and <e1> <e2> ... <en>) ; note that if one of them is false, the rest will not be evaluated
(or <e1> <e2> ... <en>) ; note that if one of them is true, the rest will not be evaluated
(not <e>)
```

Note that `and` and `or` are special forms, not procedures, because the subexpressions are not necessarily all evaluated. `not` is a procedure.

#### Exercise 1.1-1.5

- 1.1

  ```scheme
  10
  :10
  
  (+ 5 3 4)
  :12
  
  (- 9 1)
  :8
  
  (/ 6 2)
  :3
  
  (+ (* 2 4) (- 4 6))
  :6
  
  (define a 3)
  :
  
  (define b (+ a 1))
  :
  
  (+ a b (* a b))
  :19
  
  (= a b)
  :#f ;note that in the intepreter, we use #t and #f to represent booleans
  
  (if (and (> b a) (< b (* a b)))
  	b
  	a)
  :4
  
  (cond ((= a 4) 6)
  	((= b 4) (+ 6 7 a))
  	(else 25))
  :16
  
  (+ 2 (if (> b a) b a))
  :6
  
  (* (cond ((> a b) a)
  	((< a b) b)
  	(else -1))
  	(+ a 1))
  :16
  ```

- 1.2

  ```scheme
  (/ (+ 4 5 (- 2 (- 3 (+ 6 (/ 4 5))))) (* 3 (- 6 2) (- 2 7)))
  ```

- 1.3

  ```scheme
  (define (f a b c) (cond ((and (< a b) (< a c)) (+ (* b b) (* c c)))
                          ((and (< b a) (< b c)) (+ (* a a) (* c c)))
                          ((and (< c b) (< c a)) (+ (* b b) (* a a)))))
  ```

- 1.4 

  ```scheme
  (define (a-plus-abs-b a b)
  		((if (> b 0) + -) a b))
  ```

  Will return a plus the absolute of b.

- 1.5

  ```scheme
  (define (p) (p))
  (define (test x y)
  		(if (= x 0) 0 y))
  
  (test 0 (p))
  ```

  In applicative-order, the arguments will first be evaluated and will result in a infinite cycle.
  In normal-order, the operator will first be evaluated and become an special form if where it will return 0.

### 1.1.7 Example: Square Roots by Newton's Method

How does one compute square roots? You can use Newton's method of successive approximation. If you have a guess $y$ for the square root, you can always get a better guess $\frac{1}{2}(y+\frac{x}{y})$ .

To formalize the process, we first need to determine whether the guess is good enough or not. If yes, we return the result. If no, we improve the guess and run the check again. We can code this iteration:

```scheme
(define (sqrt-iter guess x)
	(if (good-enough? guess x)
		guess
		(sqrt-iter (improve guess x) x)))
```

A guess is improved by averaging:

```scheme
(define (improve guess x)
  (average guess (/ x guess)))

(define (average x y)
  (/ (+ x y) 2))
```

And by good enough we can code:

```scheme
(define (good-enough? guess x)
  (< (abs (- (square guess) x)) 0.001))
```

Finally we need to start the process:

```scheme
(define (sqrt x)
  (sqrt-iter 1.0 x))
```

It's done! This shows how iteration can be accomplished using no special construct other than the ordinary ability to call a procedure. (About efficiency see tail recursion)

#### Exercise 1.6-1.8

- 1.6
  Why can't you just define an ordinary procedure in terms of `cond` to replace if? Like:

  ```scheme
  (define (new-if predicate then-clause else-clause)
    (cond (predicate then-clause)
          (else else-clause)))
  ```

  Because if you try to do this with the new-if:

  ```scheme
  (define (sqrt-iter guess x)
  	(new-if (good-enough? guess x)
  		guess
  		(sqrt-iter (improve guess x) x)))
  
  ;Aborting!: maximum recursion depth exceeded
  ```

  remember in a normal procedure, scheme uses applicative-order, which means that all operands are evaluated first. Although inside the procedure body it uses `cond`, the intepreter is still trying to completely evaluate the else-clause (an iterative clause) no matter the value of `good-enough?`, leading to maximum recursion depth exceeded.
  So always keep in mind that when you define a procedure, all the parameters will be evaluated until it's primitive, no matter what your body code might be. Note that in MIT scheme, the sequence of evaluating paramters are from right to left, meaning:

  ```scheme
  (new-if #t (display "good") (display "bad"))
  :badgood
  ```

  Not `goodbad`.

- 1.7

  ```scheme
  (define (sqrt-iter guess x)
  	(if (good-enough? guess (improve guess x) x)
  		(improve guess x)
  		(sqrt-iter (improve guess x) x)))
  
  (define (improve guess x)
    (average guess (/ x guess)))
  
  (define (average x y)
    (/ (+ x y) 2))
  
  (define (good-enough? old-guess new-guess x)
    (< (/ (abs (- old-guess new-guess)) x) 0.001))
  
  (define (sqrt x)
    (sqrt-iter 1.0 x))
  ```

  The key here is in line 2 where we are able to compare two consecutive guesses `guess` and `improve(guess x)`.

- 1.8

  ```scheme
  (define (cbrt-iter guess x)
  	(if (good-enough? guess (improve guess x) x)
  		(improve guess x)
  		(cbrt-iter (improve guess x) x)))
  
  (define (improve y x)
    (/ (+ (/ x (* y y))
       (* 2 y)) 3))
  
  (define (good-enough? old-guess new-guess x)
    (< (/ (abs (- old-guess new-guess)) x) 0.001))
  
  (define (cbrt x)
    (cbrt-iter 1.0 x))
  ```

  Note that the Lisp way of calling functions are not `improve(guess x)` but `(improve guess x)`. It's kind of confusing.

### 1.1.8 Procedures as Black-Box Abstractions

In our `sqrt` process, we decomposed the procedure into separate parts—where each of the sub-procedures can be deemed as black-box abstractions when called by another sub-procedure.
A user should not need to know how the procedure is implemented in order to use it.

Local names should also not matter in a procedural acstraction. That is, the following procedures should be indisguishiable:

```scheme
(define (square x) (* x x))
(define (square y) (* y y))
```

The formal parameters of a procedure is a *bound variable*, and the procedure definition `binds` its formal parameters. The set of expressions for which a binding defines a name is called the *scope* of that name. If a variable is not bound (that is, it being global) it's called a *free* variable.

Another way we can achieve name isolation is by localizing subprocedures. In our previos code, although the only procedure that matters to the user is `sqrt`, we are also taking the names of `good-enough?` and `improve`, which we cannot use later on. Therefore, we could *localize* the subprocedures by doing this:

```scheme
(define (sqrt x)
	(define (good-enough? guess x)
		(< (abs (- (square guess) x)) 0.001))
	(define (improve guess x) (average guess (/ x guess)))
	(define (sqrt-iter guess x)
		(if (good-enough? guess x)
			guess
			(sqrt-iter (improve guess x) x)))
(sqrt-iter 1.0 x))
```

Such nesting of definition is called *block structure*. There's another benefit of doing this: since `x` is always bound in the definition of `sqrt`, and now all the subprocedures are also in the definition of `sqrt`, we do not have to pass `x` as a parameter to these subprocedures anymore: we allow `x` to be a free variable in the internal definitions. Such discipline is called *lexical scoping* (It states that free variables in a procedure are looked up in the environment in which the procedure was defined.)

## 1.2 Procedures and the Processes They Generate

We have learned the elements of programming:

- Primitive arithmetic operations
- Combining operations
- Abstracting composite operations by defining them as procedures

But we are now like a 600-ELO player on chess.com: we know all the rules (except maybe en passant), but we don't yet know the common patterns of usage. We now must learn to visualize the processes generated by procedures.

In this section, we will examine common "shapes" for processes generated by simple procedures and the rate they consume time and space.

### 1.2.1 Linear Recursion and Iteration

There are many ways of computing factorials. One of them utilize the observation $n!=n\times (n-1)!$  
```scheme
(define (factorial n)
  (if (= n 1)
      1
      (* n (factorial (- n 1)))))
; this is a linear recursive process for computing factorials
```

Using the substitution model, the whole process will look like this (same in both  *applicative* and *normal* order):

```scheme
(factorial 6)
(* 6 (factorial 5))
(* 6 (* 5 (factorial 4)))
(* 6 (* 5 (* 4 (factorial 3))))
(* 6 (* 5 (* 4 (* 3 (factorial 2)))))
(* 6 (* 5 (* 4 (* 3 (* 2 (factorial 1))))))
(* 6 (* 5 (* 4 (* 3 (* 2 1)))))
(* 6 (* 5 (* 4 (* 3 2))))
(* 6 (* 5 (* 4 6)))
(* 6 (* 5 24))
(* 6 120)
720 
```

Another way is to first maintain a running product by multiplying 1 and 2, and then 3, 4 etc. We can describe the process using two numbers:

```pseudocode
product <-- counter * product
counter <-- counter + 1
```

And stipulating that $n!$ = `product` when `counter > n`. We can describe this as:

```scheme
(define (factorial n)
	(define (fact-iter product counter max-count)
		(if (> counter max-count)
			product
			(fact-iter (* counter product) (+ counter 1) max-count)))
  	(fact-iter 1 1 n)) ; Note that we don't really need the max-count because n is defined inside the factorial procedure.
```

The process will look like this:

```scheme
(factorial 6)
(fact-iter 1 1 6)
(fact-iter 1 2 6)
(fact-iter 2 3 6)
(fact-iter 6 4 6)
(fact-iter 24 5 6)
(fact-iter 120 6 6)
(fact-iter 720 7 6)
720
; this is a linear iterative process
```

Let's look at the recursive process. The procedure will return `(* n (factorial (- n 1))`), and because we must first evaluate the arguments, the procedure `factorial` will be evaluated again before any primitive operation is done (except `(- n 1)`). This cause the process to build up a chain of *deferred operations*. We need space to keep track of this kind of chains, so the amount of information needed to keep track of it grows linearly with $n$, and so does the number of steps. We call this a *linear recursive process*.

---

On the other hand, look at the second process. As the process goes on, the process does not grow and shrink; all we need to keep track of is *product, counter, max-count*. We call this an *iterative process*, whose state can be summarized by a fixed number of *state variables*, together with a fixed rule that describes how the state variables should be updated as the process moves from state to state. In `factorial`, the number of steps grows linearly with $n$, so it's called a *linear iterative process.*

> Side note: note how in the recursive process, the `factorial` procedure is an operand on its own. And in the iterative process, the `factorial` procedure itself is the returned value. This is a quick way to distinguish these two processes

There's a difference between *recursive procedure* and *recursive process.* A *recursive procedure* is a syntactic fact that the procedure definition contains the procedure itself (both the iterative and the recursive implementations of the `factorial` procedure are recursive procedures), while *recursive process* denotes how the process evolves, not the syntax.

However, in most common languages, a *recrusive procedure* that's iterative will also consumes memory that grows with the number of procedure calls (instead of a constant that we predicted above). As a result, these languages uses special "looping constructs" (i.e. `do, repeat, until, for, while`). Scheme does not share this defect— it's implementation is *tail-recursive*, meaning it will execute an iterative process in constant space.

#### Exercise 1.9-1.10

- 1.9

```scheme
(define (+ a b)
(if (= a 0) b (inc (+ (dec a) b)))) ; this process is recursive

: (+ 4 5)
(inc (+ 3 5))
(inc (inc (+ 2 5)))
(inc (inc (inc (+ 1 5))))
(inc (inc (inc (inc (+ 0 5)))))
(inc (inc (inc (inc 5))))
...
9

(define (+ a b)
(if (= a 0) b (+ (dec a) (inc b)))) ; this process is iterative

: (+ 4 5)
(+ 3 6)
(+ 2 7)
(+ 1 8)
(+ 0 9)
9
```

- 1.10

```scheme
(define (A x y)
(cond ((= y 0) 0)
	  ((= x 0) (* 2 y))
	  ((= y 1) 2)
	  (else (A (- x 1) (A x (- y 1))))))
```

Values of:

```scheme
(A 1 10)
(A 0 (A 1 9))
(A 0 (A 0 (A 1 8))...
= 2^10 =  1024
```
It is easy to see that (A 1 n)=2^n
```scheme
(A 2 4)
(A 1 (A 2 3))...
= 2^2^2^2 = 65536
```
Now we know that (A 2 n)=2^(A 2 n-1) and (A 2 1)=2, so (A 2 n) =  2 ^ 2 ^ 2 ^ 2…

```scheme
(A 3 3)
= 65536 ;  I'm not even going to try to intepret that. It's hyperoperation
```

```scheme
(define (f n) (A 0 n)) ; this will be 2*n
(define (g n) (A 1 n)) ; this will be 2^n
(define (h n) (A 2 n)) ; this will be 2^2^2^2... for n times
```

### 1.2.2 Tree Recursion

Other than *linear recursion*, we also have *tree recursion.* For example, try and calculate the Fibonacci numbers:

```scheme
(define (fib n)
  (cond ((= n 0) 0)
        ((= n 1) 1)
        (else (+ (fib (- n 1)) (fib (- n 2))))))
```

This is a terrible way of calculating a fibonacci number. The evolve process looks like a tree, and all the branches split into two at each level. In fact, the tree will end up having a $Fib(n+1)$ number of leaves(can you prove this? **P: subsitute all Fib(x) to Fib(x+1) and now all leaves have value 1**)—this is exponential since $Fib(n)$ is the closest integer to $\varphi^n / \sqrt{5}$ 

We can also use an iterative process for computing the fibonacci numbers:

```scheme
(define (fib n)
  (fib-iter 1 0 n))
(define (fib-iter a b count)
  (if (= count 0)
      b
      (fib-iter (+ a b) a (- count 1))))
```

One should not conclude that *tree recursion* is useless. For example the interpreter itself evaluates expressions using a tree-recursive process. Also, recursive procedures are often more intuitive.

- Example

  Let's consider how to count the different ways we could make change of a certain amount of dollars(`a`) with half-dollars, quarters, dimes, nickels and pennies. Obviously, for any kind of coins, the ways to make change can be divided into two catagories:

  - Those ways that doesn't uses the certain kind of coins
  - Those ways that uses (at least once!) the certain kind of coins

  Obviously the second catagories can be tranformed into

  - Ways that you can make change of `a-d` dollars, where d is the denomination of that certain kind of coin

  Now we can use recursion to solve the problem.

  Consider the edge cases:

  - When there's no kind of coins left or total amount(`a-d`) is negative, there are zero ways
  - When the coin's denomination equals the total amount of dollars, there are exactly one ways.

  Let's code:

  ```scheme
  (define (count-change amount) (cc amount 5))
  (define (cc amount kinds-of-coins)
  	(cond ((= amount 0) 1)
  			((or (< amount 0) (= kinds-of-coins 0)) 0)
  			(else (+ (cc amount
  					 (- kinds-of-coins 1))
  					 (cc (- amount
  							(first-denomination
  							kinds-of-coins))
  							kinds-of-coins)))))
  (define (first-denomination kinds-of-coins)
  	(cond ((= kinds-of-coins 1) 1)
  		((= kinds-of-coins 2) 5)
  		((= kinds-of-coins 3) 10)
  		((= kinds-of-coins 4) 25)
  		((= kinds-of-coins 5) 50)))
  ```

  Note that the `cond` in `first-denomination` can be of any order.

  Can you do this in a iterative process?

  ```scheme
  (define (count-change-iterative amount)
    ;; penny is not in the signiture, bacause it equals (- amount
    ;;                                                     (* half-dollar 50)
    ;;                                                     (* quarter 25)
    ;;                                                     (* dime 10)
    ;;                                                     (* nickeli 5))
    (define (count-iter half-dollar quarter dime nickeli)
      (cond ((> (* half-dollar 50) amount)
             0)
            ((> (+ (* half-dollar 50)
                   (* quarter 25)) amount)
             (count-iter (+ half-dollar 1) 0 0 0))
            ((> (+ (* half-dollar 50)
                   (* quarter 25)
                   (* dime 10)) amount)
             (count-iter half-dollar (+ quarter 1) 0 0))
            ((> (+ (* half-dollar 50)
                   (* quarter 25)
                   (* dime 10)
                   (* nickeli 5)) amount)
             (count-iter half-dollar quarter (+ dime 1) 0))
            (else (+ 1
                     (count-iter half-dollar quarter dime (+ nickeli 1))))))
    (count-iter 0 0 0 0))
  ```

  This is not mine, but look at this beautiful code please.



