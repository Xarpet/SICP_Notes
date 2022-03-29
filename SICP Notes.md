[toc]

# Preface: Resources

- [Companion website by MIT](https://mitpress.mit.edu/sites/default/files/sicp/index.html)
- [中文版题解](https://sicp.readthedocs.io/en/latest/)
- [SICP-solutions](http://community.schemewiki.org/?SICP-Solutions)
- How to import codes into your program:
  `(#%require "lib.scm")`

# 1 Building Abstractions with Procedures

Through out the book we will use `Scheme`, a dialect of lisp to program.

## 1.1 The Elements of Programming

> A powerful programming language is more than just a means for instructing a computer to perform tasks. The language also serves as a framework within which we organize our ideas about processes.

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

If a combination has an operator that is a compound procedure, the interpreter must follow a certain sequence of process to evaluate the operators and the operands. More specifically, the formal parameters in the body of the procedure (operator) needs to be substituted by the operands(whether already evaluated to primitive objects or still remains to be expressions. See below.)

The most intuitive and what lisp uses is *applicative-order evaluation*, where all the operands are fully evaluated to primitive objects before substituting the formal parameters in the procedure. 

```scheme
(sum-of-squares (+ 2 1) (- 5 1))
(sum-of-squares 3 4)
(+ (square 3) (square 4))
(+ (* 3 3) (* 4 4))
(+ 9 16)
25 ;applicative-order evaluation
```

Another type of evaluation strategy is called *normal-order evaluation* (there are more) where you do not evaluate **any** of the arguments until they are needed in the body of the function (or obtained an expression involving only primitive operators).

```scheme
(sum-of-squares (+ 2 1) (- 5 1))
(+ (square (+ 2 1)) (square (- 5 1)))
(+ (* (+ 2 1) (+ 2 1)) (* (- 5 1) (- 5 1)))
(+ (* 3 3) (* 4 4))
(+ 9 16)
25 ;normal-order evaluation
```

Lisp uses *applicative-order evaluation* It can be proved that, for legitimate procedures, two strategies will yield the same result.

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
Predicates are procedures that return true or false (in Lisp interpreter, they are respectively `#t` and `#f`).

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

Note that `and` and `or` are special forms, not procedures, because **the sub-expressions are not necessarily all evaluated**. `not` is a procedure.

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

And to determine whether the guess is good enough:

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

Local names should also not matter in a procedural abstraction. That is, the following procedures should be indistinguishable:

```scheme
(define (square x) (* x x))
(define (square y) (* y y))
```

The formal parameters of a procedure is a *bound variable*, and the procedure definition `binds` its formal parameters. The set of expressions for which a binding defines a name is called the *scope* of that name. If a variable is not bound (that is, it being global) it's called a *free* variable.

Another way we can achieve name isolation is by localizing sub procedures. In our previous code, although the only procedure that matters to the user is `sqrt`, we are also taking the names of `good-enough?` and `improve`, which we cannot use later on. Therefore, we could *localize* the sub-procedures by doing this:

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

Such nesting of definition is called *block structure*. There's another benefit of doing this: since `x` is always bound in the definition of `sqrt`, and now all the sub-procedures are also in the definition of `sqrt`, we do not have to pass `x` as a parameter to these sub-procedures anymore: we allow `x` to be a free variable in the internal definitions. Such discipline is called *lexical scoping* (It states that free variables in a procedure are looked up in the environment in which the procedure was defined.)

## 1.2 Procedures and the Processes They Generate

We have learned the elements of programming:

- Primitive arithmetic operations
- Combining operations
- Abstracting composite operations by defining them as procedures

But we are now like a 600-ELO player on chess.com: we know all the rules (except maybe *en passant*), but we don't yet know the common patterns of usage. We now must learn to visualize the processes generated by procedures.

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

This is a terrible way of calculating a Fibonacci number. The evolve process looks like a tree, and all the branches split into two at each level. In fact, the tree will end up having a $Fib(n+1)$ number of leaves(can you prove this? **P: substitute all Fib(x) to Fib(x+1) and now all leaves have value 1**)—this is exponential since $Fib(n)$ is the closest integer to $\varphi^n / \sqrt{5}$ 

We can also use an iterative process for computing the Fibonacci numbers:

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

#### Exercise 1.11-1.13

- 1.11
    $$
    f(n)=\begin{cases} n &if &n <3\\
    f(n-1)+2f(n-2)+3f(n-3) & if & n\ge 3 \end{cases}
    $$
    The recursive process:

    ```scheme
    (define (nfib n)
      (if (< n 3) n 
          (+ (nfib (- n 1)) (* 2 (nfib (- n 2))) (* 3 (nfib (- n 3))))))
    ```

    The iterative process:

    ```scheme
    (define (nfib n) (define (nfib-iter a b c count)
      (cond ((< n 3) n)
            ((= count n) a)
            (else 
             (nfib-iter b c (+ c (* 2 b) (* 3 a)) (+ count 1)))))
       (nfib-iter 1 2 4 1))
    ```

    It is obvious that the iterative version is more time efficient

- 1.12
    Pascal's triangle using recursive process:

    ```scheme
    (define (pascal row col)
      (cond ((or (= col 0) (= col row))
            1)
            (else (+ (pascal (- row 1) col)
              	 	(pascal (- row 1) (- col 1))))))
    ```
    
    Can you please pay attention to add `else` because it took me literally for ever to find out.
    
    Using Iterative process: Please just use the Binomial Theorem
    
- 1.13

    Prove that $Fib(n)$ is the closest integer to  $\varphi^n / \sqrt{5}$ .

    Let $\psi := (1-\sqrt{5})/2$. Now we are going to prove $Fib(n)=(\varphi^n-\psi^n)/\sqrt{5}$ using (double) induction:
    $$
    \text{For }n=1\text{ or }2, Fib(n)=(\varphi^n-\psi^n)/\sqrt{5}; \\ 
    \text{For }n>2, Fib(n)=Fib(n-1)+Fib(n-2) \\
    =\frac{(\varphi^{n-1}-\psi^{n-1})}{\sqrt{5}}+\frac{(\varphi^{n-2}-\psi^{n-2})}{\sqrt{5}}\\
    =\frac{(\varphi^{n-2}(1+\varphi))-(\psi^{n-2}(1+\psi))}{\sqrt{5}}\\
    \text{since }\varphi+1=\varphi^2,\psi+1=\psi^2\\
    Fib(n)=(\varphi^n-\psi^n)/\sqrt{5} \\Q.E.D
    $$
    It is clear that $\psi^n/\sqrt{5}<1/2$ for all $n$, so $Fib(n)$ is the closest integer to $\varphi^n/\sqrt{5}$

### 1.2.3 Orders of Growth

Let's use $R(n)$ to denote the amount of resources in general a certain process requires to solve a problem of size $n$. 

We say that $R(n)$ has order of growth $\Theta(f(n))$, i.e. $R(n)=\Theta(f(n))$, when there are positive constants $k_1$ and $k_2$ independent of $n$ such that $k_1f(n)\le R(n) \le k_2f(n)$ for any sufficiently large value of n.

Some examples: For the iterative factorial, the number of steps is $\Theta(n)$ while the space is $\Theta(1)$ (a constant). For the tree-recursive Fibonacci, steps is $\Theta(\varphi^n)$ while space is $\Theta(n)$

While orders of growth dismisses the $k$ constants and are crude, they are very useful.

#### Exercise 1.14-1.15

-   1.14
    Not shown here.

    Order of growth for space is clearly $\Theta(n)$ since this is recursive process and the longest call will be all pennies.

    Order of growth for steps is actually $\Theta(n^k)$ where $k$ is the total kinds of coins. You can see why that is the case [here](https://codology.net/post/sicp-solution-exercise-1-14/)

-   1.15

    ```scheme
    (define (cube x) (* x x x))
    (define (p x) (- (* 3 x) (* 4 (cube x))))
    (define (sine angle)
    	(if (not (> (abs angle) 0.1))
    		angle
    		(p (sine (/ angle 3.0)))))
    ```

    a.:
    $$
    log_{\frac{1}{3}}(\frac{0.1}{12.15})\approx4.36
    $$
    So five times. A tip, you can do this in scheme to track the calling of a certain procedure:

    ```scheme
    (trace-entry p)
    ```

    b.:
    $$
    log_{\frac{1}{3}}(\frac{0.1}{a})=log_3(10a)
    $$
    So the number of steps is $\Theta(\log{a})$, and because it's recursive process, the space is also $\Theta(\log{a})$

### 1.2.4 Exponentiation

We can use calculate exponentials using recursive process:

```scheme
(define (expt b n)
	(if (= n 0)
		1
		(* b (expt b (- n 1)))))
```

Clearly time and space is both $\Theta(n)$. The iterative version of this

```scheme
(define (expt b n)
	(expt-iter b n 1))
(define (expt-iter b counter product)
	(if (= counter 0)
		product
		(expt-iter b
				   (- counter 1)
				   (* b product))))
```

has $\Theta(n)$ steps and $\Theta(1)$ space.

We can compute faster using successive squaring. For instance, instead of multiplying eight times for $b^8$, we can instead use only three multiplications:
$$
b^2=&b\times b\\
b^4 =& b^2\times b^2 \\ 
b^8=&b^4\times b^4
$$
For odds we just follow the old way.

```scheme
(define (fast-expt b n)
	(cond ((= n 0) 1)
		((even? n) (square (fast-expt b (/ n 2))))
		(else (* b (fast-expt b (- n 1))))))
(define (even? n)
  (= (remainder n 2) 0))
```

Both the steps and space is $\Theta(\log{n})$ here; observe how computing $b^{2n}$ only takes one more step than $b^n$.

#### Exercise 1.16-1.19

-   1.16
    
    ```scheme
    (define (expt b n)
    	(expt-iter b n 1))
    (define (expt-iter b exponent product)
    	(cond ((= exponent 0) product)
                  ((even? exponent) (expt-iter (* b b) (/ exponent 2) product))
                  (else (expt-iter b (- exponent 1) (* product b)))))
    ```
    
    This is actually a bit tricky, as you can only "square" b by making directly squaring the base—I mean, you cannot do `(square (expt-iter b (\ exponent 2) product))` 'cause that would make the process recursive.
    
    An *invariant identity* (here it is $product*b^n=constant$ throught out the process) is often very important when designing iterative processes.
    
-   1.17
    
    ```scheme
    (define (* a b)
    	(if (= b 0)
    		0
    		(+ a (* a (- b 1)))))
    ```
    
    above is the original
    
    ```scheme
    (define (* a b)
      (define (double x) (+ x x))
      (define (halve x) (/ x 2))
      (cond ((= b 0) 0)
            ((even? b) (* (double a) (halve b)))
            (else (+ a (* a (- b 1))))
            ))
    ```
    
-   1.18
    The iterative version:

    ```scheme
    (define (* a b)
      (define (double x) (+ x x))
      (define (halve x) (/ x 2))
      (define (*-iter a b acc)
        (cond ((= b 0) acc)
              ((even? b) (*-iter (double a) (halve b) acc))
              (else (*-iter a (- b 1) (+ acc a)))
              ))
      (*-iter a b 0))
    ```

-   1.19

    We exert $T_{pq}$ twice:
    $$
    a_1=b_0q+a_0(q+p)\\
    b_1=b_0p+a_0q \\
    a_2=b_1q+a_1(q+p)=b_0qp+a_0q^2+b_0(q^2+qp)+a_0(q^2+2qp+p^2)\\
    =b_0(q^2+2qp)+a_0(2q^2+2qp+p^2)\\
    b_2=b_1p+a_1q=b_0p^2+a_0qp+b_0q^2+a_0(q^2+qp)\\
    =b_0(p^2+q^2)+a_0(q^2+2qp)
    $$
    It is evident that
    $$
    p'=(p^2+q^2)\\
    q'=(q^2+2qp)
    $$
    Therefore, whenever `N` (rest of the way to go) is even, we can square this transformation and cut it down in half (because we are walking two steps at once now.)
    
    ```scheme
    (define (fib n)
        (fib-iter 1 0 0 1 n))
    
    (define (fib-iter a b p q n)
        (cond ((= n 0)
                b)
              ((even? n)
                (fib-iter a 
                          b
                          (+ (square p) (square q)) ;Compute p'
                          (+ (* 2 p q) (square q)) ;Compute q'
                          (/ n 2)))
              (else
                (fib-iter (+ (* b q) (* a q) (* a p))
                          (+ (* b p) (* a q))
                          p
                          q
                          (- n 1)))))
    ```
    

### 1.2.5 Greatest Common Divisors

When calculating GCD, we can utilize *Euclid's Algorithm*, which states:
$$
GCD(a,b)=GCD(b,a\space mod \space b)
$$
(辗转相除法)
This is because:
$$
a-kb=r\\
\text{Assuming d is a common divisor of a and b}\\
a/d-kb/d=r/d\\
\text{Obviously d is also the divisor of r.}
$$
Let's write down the procedure:
```scheme
(define (gcd a b)
  (if (= b 0)
      a
      (gcd b (remainder a b))))
```

This is an iterative process whose time complexity is $\Theta(\log{x})$. This fact is proved using *Lamé’s Theorem*:

>   If Euclid’s Algorithm requires k steps to compute the GCD of some pair, then the smaller number in the pair must be greater than or equal to the k th Fibonacci number.

Proof of this is trivial, just consider pairs $(a_k,b_k)$, where k is the number of steps required for the Euclid's Algorithm to terminate. Obviously, we have $a_k=qb_k+b_{k-1}$, where $q\ge 1$. Therefore $b_{k+1}\ge b_k+b_{k-1}$. Using induction we can prove the theorem.
Therefore if $n$ is the smaller number, and the process requires $k$ steps, we have $n\ge Fib(k)\approx \varphi^k/\sqrt{5}$, which means that the time order of growth is $\Theta(\log{n})$.

#### Exercise 1.20

-   1.20

    ```scheme
    (define (gcd a b)
    	(if (= b 0)
    	a
    	(gcd b (remainder a b))))
    ; in the normal-order
    gcd(206, 40)
    gcd(40, (remainder 206 40))
    gcd((remainder 206 40), (remainder 40 (remainder 206 40))); here intepreter evaluated (remainder 206 40)
    gcd((remainder 40 (remainder 206 40)), (remainder (remainder 206 40) (remainder 40 (remainder 206 40)))); here intepreter evaluated (remainder 40 (remainder 206 40))
    gcd((remainder (remainder 206 40) (remainder 40 (remainder 206 40))), (remainder (remainder 40 (remainder 206 40)) (remainder (remainder 206 40) (remainder 40 (remainder 206 40)))); here the intepreter evaluated (remainder (remainder 206 40) (remainder 40 (remainder 206 40)))
    (remainder (remainder 206 40) (remainder 40 (remainder 206 40))); here the intepreter evaluated (remainder (remainder 40 (remainder 206 40)) (remainder (remainder 206 40) (remainder 40 (remainder 206 40)))) and is zero.
    2
    ```
    
    Normal-order: For every step, the number of remainder calls grows with the Fibonacci series, so the total calls should be $\approx \sum_{n=1}^{k}{Fib(n)}$ where $k$ is the total number of steps.
    Applicative-order: $k$ times

### 1.2.6 Example: Testing for Primality

One way to test the primality of a number is to find its divisors.

```scheme
(define (smallest-divisor n) (find-divisor n 2))
(define (find-divisor n test-divisor)
	(cond ((> (square test-divisor) n) n)
		((divides? test-divisor n) test-divisor)
		(else (find-divisor n (+ test-divisor 1)))))
	(define (divides? a b) (= (remainder b a) 0))

(define (prime? n)
  (= n (smallest-divisor n)))
```

The first `cond` statement utilize the obvious fact that a non-self divisor of a number is always less than or equal to its square root. Therefore the time complexity is $\Theta(\sqrt{n})$

Another way to test promality is to utilize the *Fermat's Little Theorem*:

> **Fermat’s Little Theorem:** If $n$ is a prime number and $a$ is any positive integer less than $n$, then 
> $$
> a^n\equiv a\mod{n}
> $$

To say in conditions:

> If $a<n$ and $a^n\not\equiv a\mod{n}$,
>
> $n$ is not prime.

 Note that we need to test multiple numbers to make sure that $n$ is prime.

We also need a procedure that computes the exponential of a number modulo another number:

```scheme
(define (expmod base exp m)
	(cond ((= exp 0) 1)
			((even? exp)
			(remainder
			(square (expmod base (/ exp 2) m))
			m))
	(else
		(remainder
		(* base (expmod base (- exp 1) m))
		m))))
```

This is based on the fact that
$$
x*y\mod{m}=[(x\mod{m})*(y\mod{m})]\mod{m}
$$
So
$$
a^b\mod{m}=\begin{cases}
((a\mod{m})*(a^{b-1}\mod{m}))\mod{m}, &\text{when }b\text{ is odd}  \\
(a^{b/2}\mod{m})^2\mod{m}, &\text{when }b\text{ is even}
\end{cases}
$$
That way we don't have to deal with enourmous numbers. The procedure is:

```scheme
(define (fast-prime? n times)
  (define (fermat-test n)
	(define (try-it a)
		(= (expmod a n n) a))
    
  	(try-it (+ 1 (random (- n 1)))))
  
  (cond ((= times 0) true)
		((fermat-test n) (fast-prime? n (- times 1)))
		(else false)))
```

where `random` returns a nonnegative integer less than its parameter. This algorithm has a time complexity of $\Theta({\log{n}})$

---

Here, the Fermat test is different from most familiar algorithms: it is not guaranteed to be correct. Besides the fact that testing not enough times will not give a 100% true result, there are also non-prime integers called *Carmichael numbers* who will never fail the Fermat test for all integers less than itself.

#### Exercise 1.21-1.28

- 1.21

  ```scheme
  (smallest-divisor 199)
  :199
  (smallest-divisor 1999)
  :1999
  (smallest-divisor 19999)
  :7
  ```

- 1.22
  `runtime` is a function without parameter that returns the running time. Note that `runtime` uses second as unit. `real-time-clock` uses milisecond.
  This exercise is really stupid and the solution is trivial. The answers are no, no and no.

- 1.23

  ```scheme
  (define (next x)
    (cond ((= x 2) 3)
          (else (+ x 2))))
  ```

  The answer is still no.
  **Note** that in Scheme, redefining a function will overwrite its definition.

- 1.24
  Modifying this is trivial
  The actual runtime depends on a lot of things, and time complexity doesn't always accurately predict the runtime. However, its trend is almost always correct.

- 1.25
  As usual she is wrong
  The problem is that sometimes the result of directly calculating the exponential is enormous and will cause overflow. Hoever the `expmod` procedure will limit the result to be less than $m$ every time because the remainder is calculated.

- 1.26
  The thing is, in an applicative-order process, all parameters are first calculated before being substituted into the procedure. So in `(square (expmod base (/ exp 2) m))` the `expmod` is only called once, while in `(remainder (* (expmod base (/ exp 2) m) (expmod base (/ exp 2) m)) m)` the `expmod` is called twice. This basically render the whole even case useless.

- 1.27

  ```scheme
  (define (fermat-test n)
    (define (iter-fermat-test a)
      (cond ((= a n) #t)
            ((= (expmod a n n) a) (iter-fermat-test (+ a 1)))
  		  (else #f)))
    (iter-fermat-test 1))
  ```

  ```scheme
  > (fermat-test 561)
  #t
  > (fermat-test 1105)
  #t
  > (fermat-test 2465)
  #t
  > (fermat-test 2821)
  #t
  > (fermat-test 6601)
  #t
  ```

- 1.28

  ```scheme
  (define (square-remainder-test x m)
    (cond ((or (= x (- m 1)) (= x 1)) (remainder (square x) m))
          ((= (remainder (square x) m) 1) 0)
          (else (remainder (square x) m))))
        
        
  (define (expmod base exp m)
    (cond ((= exp 0) 1)
          ((even? exp)
           (square-remainder-test (expmod base (/ exp 2) m) m))
          (else
           (remainder (* base (expmod base (- exp 1) m))
                      m))))        
  
  (define (fermat-test n)
    (define (iter-fermat-test a)
      (cond ((= a n) #t)
            ((= (expmod a (- n 1) n) 1) (iter-fermat-test (+ a 1)))
            (else #f)))
    (iter-fermat-test 1))
  ```

  ```scheme
  > (fermat-test 561)
  #f
  > (fermat-test 1105)
  #f
  > (fermat-test 1999)
  #t
  ```


  ## 1.3 Formulating Abstractions with Higher-Order Procedures

  Who said procedures can only take numbers and spit out numbers? Now we are going to examine *higher-order procedures*, which can take other procedures as parameters and return procedures as values.

  ### 1.3.1 Procedures as Arguments

  Consider the following procedures:
  ```scheme
  (define (sum-integers a b)
    (if (> a b)
        0
        (+ a (sum-integers (+ a 1) b))))
  
  (define (sum-cubes a b)
    (if (> a b)
        0
        (+ (cube a) (sum-cubes (+ a 1) b))))
  
  (define (pi-sum a b)
    (if (> a b)
        0
        (+ (/ 1.0 (* a (+ a 2))) (pi-sum (+ a 4) b))))
  ```

  Which are respectively:
$$
  \sum^{b}_{x=a}x \\
  \sum^{b}_{x=a}x^3 \\
  \sum^{b}_{x=a}\frac{1}{(4x-3)*(4x-1)}\approx \frac{\pi}{8}
$$
  We want to make abstraction of the **summation sign** because that is what these three procedures all have in common. For a summation process of this kind:
$$
  \sum^b_{x=a}f(x)
$$
  As a programmer, we would like our language to be powerful enough so that we can write a procedure that expresses the concept of summation itself rather than only procedures that computes particular sums.

  ```scheme
  (define (sum term a next b)
    (if (> a b)
        0
        (+ (term a)
           (sum term (next a) next b))))
  ```

  Where `term` is the procedure that defines $f(x)$, and `next` is the increment of each step. They are both procedures.

  Now we can use it to define all three procedures:
  ```scheme
  (define (inc n) (+ n 1))
  
  (define (identity x) x)
  (define (sum-integers a b)
    (sum identity a inc b))
  
  (define (sum-cubes a b)
    (sum cube a inc b))
  
  (define (pi-sum a b)
    (define (pi-term x)
      (/ 1.0 (* x (+ x 2))))
    (define (pi-next x)
      (+ x 4))
    (sum pi-term a pi-next b))
  ```

  Once we have `sum`, we can define more concepts like *definite integrals*. Using this formula:
$$
  \int_a^bf=[f(a+\frac{dx}{2})+f(a+dx+\frac{dx}{2})+f(a+2dx+\frac{dx}{2})+...]dx
$$

  ```scheme
  (define (integral f a b dx)
    (define (add-dx x) (+ x dx))
    (* (sum f (+ a (/ dx 2)) add-dx b)
       dx))
  ```

  #### Exercise 1.29-1.33

- 1.29

  ```scheme
  (define (simpson f a b n)
    (define (coe x y)
      (cond ((or (= x 0) (= x y)) 1)
            ((even? x) 2)
            (else 4)))
    (define (iter-simpson count sum)
      (if (> count n)
          sum
          (iter-simpson (+ count 1) (+ sum (* (/ 1 3) (/ (- b a) n) (* (coe count (/ (- b a) n)) (f (+ a (* count (/ (- b a) n))))))))))
    (iter-simpson 0 0))
  ```

  The accruacy of my solution is terrible and I do not intend to find out why.

- 1.30

  ```scheme
  (define (sum term a next b)
    (define (iter-sum x acc)
    (if (> x b)
        acc
        (iter-sum (next x) (+ acc (term x)))))
    (iter-sum a 0))
  ```

- 1.31

  ```scheme
  (define (product term a next b)
    (define (iter-sum x acc)
    (if (> x b)
        acc
        (iter-sum (next x) (* acc (term x)))))
    (iter-sum a 1))
  ```

- 1.32

  ```scheme
  (define (accumulate combiner null-value term a next b)
    (define (iter-acc x acc)
    (if (> x b)
        acc
        (iter-acc (next x) (combiner acc (term x)))))
    (iter-acc a null-value))
  ```

- 1.33

  ```scheme
  (define (filtered-accumulate filter combiner null-value term a next b)
    (define (iter-acc x acc)
    (if (> x b)
        acc
        (if (filter x)
            (iter-acc (next x) (combiner acc (term x)))
            (iter-acc (next x) acc)))
    (iter-acc a null-value)))
  ```

  ```scheme
  (filtered-accumulate prime? + 0 square a inc b)
  ```

  ```scheme
  (filtered-accumulate (lambda (x) (= (gcd x n) 1)) * identity 1 inc (- n 1))
  ```

### 1.3.2 Constructing Procedures Using `lambda`

As we have discovered, the ability to name an anomynous procedure will be very useful when the procedure is very simple. It doesn't infringe our valuable name space.
Using `lambda` we can do that.

```scheme
(lambda (x) (+ x 1))
```

This is a way of describing `inc`. Generally the syntax of `lambda` is
```scheme
(lambda (<formal-parameters>) <body>)
```

Note that even if there is only one formal parameter you will also need a parenthesis around it. The body does not.
Like any expression that has a procedure as its value, you can use this as an operator

```scheme
((lambda (x y z) (+ x y (square z)))
 1 2 3)
:12
```

Another syntactic sugar we can utilize is `let`, which create local variables. Let's say we want to compute
$$
f(x,y)=x(1+xy)^2+y(1-y)+(1+xy)(1-y)
$$
which could be simplified by using variables $a$ and $b$:
$$
a=1+xy \\ 
b=1-y\\
f(x,y)=xa^2+yb+ab
$$
Now if we want to do that, we can use `let` which defines local variables:

```scheme
(define (f x y)
  (let ((a (+ 1 (* x y)))
        (b (- 1 y)))
    (+ (* x (square a))
       (* y b)
       (* a b))))
```

The general syntax of `let` is

```scheme
(let ((<var1> <exp1>)
      (<var2> <exp2>)
      ...
      (<varn> <expn>))
  <body>)
;consisely:
(let (<the var and expression tuples>)
<body>)
```

In reality, this syntactic sugar is intepreted as 
```scheme
((lambda (<var1> ... <varn>)
	<body>)
<exp1> <exp2> ... <expn>)
```

No new mechanism is required in the interpreter in order to provide local variables. A `let` is simply `lambda`. This implies that:

- `let` variables are entirely local. For example, if `x` is 5, then the following expression:

  ```scheme
  (+ (let ((x 3))
      	(+ x (* x 10)))
      x)
  ```

  Will still be 38.

- The variables' values are computed outside the `let` expression. To see that:
  ```scheme
  (let ((x 3)
       (y (+ x 2)))
     (* x y))
  ```

  Now we transform the expression:
  ```scheme
  ((lambda (x y) (* x y)) 3 (+ x 2))
  ```

  From this perspective we can see that the meaning of `(+ x 2)` has nothing to do with the `x` inside the `let` expression (but only the outside value of `x` which could be anything) because in applicative order `(+ x 2)` will first be evaluated.

We can still use internal definition to define local variables like
```scheme
(define (f x y)
  (define a (+ 1 (* x y)))
  (define b (- 1 y))
  (+ (* x (square a))
     (* y b)
     (* a b)))
```

However there are subtleties that we can yet understand. Note that the internal definition of procedures is always fine.

#### Exercise 1.34

- 1.34

  ```scheme
  > (f f)
  application: not a procedure;
   expected a procedure that can be applied to arguments
    given: 2
  ```

  The reason is that the interpreter will substitute `(f f)` for `(f 2)` (note that although `f` is an argument here, it doesn't get evaluated because it is already a primitive object) and then `(2 2)`. `2` is not a valid procedure and therefore cannot be an operator.

### 1.3.3 Procedures as General Methods

After abstracting computational patterns of numerical operations, we can now abstract general methods as procedures without mentioning explicitly the functions involved.

- Finding roots of equations by the half-interval method

The *half-interval* method is a powerful technique for finding roots of an equation $f(x)=0$. If we are given points $a$ and $b$ that $f(a)<0<f(b)$, then there must be a root between $a$ and $b$. If we evaluate $f((a+b)/2)$, we can further narrow the root to half the interval. The time complexity is obviously $\Theta(\log{(L/T)})$, where $L$ is the initial interval and $T$ is the error tolerance or the final interval.
```scheme
(define (search f neg-point pos-point)
  (let ((midpoint (average neg-point pos-point)))
    (if (close-enough? neg-point pos-point)
        midpoint
        (let ((test-value (f midpoint)))
          (cond ((positive? test-value)
                 (search f neg-point midpoint))
                ((negative? test-value)
                 (search f midpoint pos-point))
                (else midpoint))))))

(define (close-enough? x y)
  (< (abs (- x y)) 0.001))
```

This procedure is very fragile because the `neg-point` and the `pos-point` we initially give must have the correct sign for the procedure to work. We can add a pre-processing procedure to make sure:
```scheme
(define (half-interval-method f a b)
  (let ((a-value (f a))
        (b-value (f b)))
    (cond ((and (negative? a-value) (positive? b-value))
           (search f a b))
          ((and (negative? b-value) (positive? a-value))
           (search f b a))
          (else
           (error "Values are not of opposite sign" a b)))))
```

`error` takes arguments and print them as error messages.

- Finding fixed points of functions

A number $x$ is called a *fixed point* of function $f$ if it satisfy $f(x)=x$. For some functions, we can locate a fixed point by applying $f$ repeatedly until the value doesn't change very much:
```scheme
(define tolerance 0.00001)

(define (fixed-point f first-guess)
  (define (close-enough? v1 v2)
    (< (abs (- v1 v2)) tolerance))
  (define (try guess)
    (let ((next (f guess)))
      (if (close-enough? guess next)
          next
          (try next))))
  (try first-guess))
```

A lot of equations can be turned into a fixed point problem. For example if you want to solve $y^2=x$, you can look for the fixed point of the function $y \mapsto x/y$. Unfortunately this does not converge because it results in an infinite loop. One way to control this is to prevent the guesses from changing that much.
Generally, the way to do that is to:
$$
x=f(x)  \rarr  x=\frac{1}{2}(x+f(x))
$$
So that now we can calculate the fixed point of function $x\mapsto \frac{1}{2}(x+f(x))$. This technique is called *average damping*.

### Exercise 1.35-1.39

- 1.35
  $$
  x^2=x+1 \rarr x=1+1/x
  $$

  ```scheme
  > (fixed-point (lambda (x) (+ 1 (/ 1 x))) 1.0)
  1.6180339631667064
  ```

- 1.36

  ```scheme
  (define tolerance 0.0001)
  
  (define (fixed-point f first-guess)
    (define (close-enough? v1 v2)
      (< (abs (- v1 v2)) tolerance))
    (define (try guess)
      (display guess)
      (newline)
      (let ((next (f guess)))
        (if (close-enough? guess next)
            next
            (try next))))
    (try first-guess))
  ```

  ```scheme
  ;without average damping
  > (fixed-point (lambda (x) (/ (log 1000) (log x))) 10)
  10
  2.9999999999999996
  6.2877098228681545
  3.7570797902002955
  5.218748919675316
  4.1807977460633134
  4.828902657081293
  4.386936895811029
  4.671722808746095
  4.481109436117821
  4.605567315585735
  4.522955348093164
  4.577201597629606
  4.541325786357399
  4.564940905198754
  4.549347961475409
  4.5596228442307565
  4.552843114094703
  4.55731263660315
  4.554364381825887
  4.556308401465587
  4.555026226620339
  4.55587174038325
  4.555314115211184
  4.555681847896976
  4.555439330395129
  4.555599264136406
  4.555493789937456
  4.555563347820309
  
  ;with average damping
  > (fixed-point (lambda (x) (/ (+ x (/ (log 1000) (log x))) 2)) 10)
  10
  6.5
  5.095215099176933
  4.668760681281611
  4.57585730576714
  4.559030116711325
  4.55613168520593
  4.555637206157649
  4.55555298754564
  ```

- 1.37

  ```scheme
  (define (cont-frac n d k)
    (define (cont-frac-rec x)
      (if (> x k)
          0
          (/ (n x) (+ (d x) (cont-frac-rec (+ x 1))))))
    (cont-frac-rec 1))
  ```

  ```scheme
  (define (cont-frac n d k)
    (define (cont-frac-iter x acc)
      (if (= x 0)
          acc
          (cont-frac-iter (- x 1) (/ (n x) (+ (d x) acc)))))
    (cont-frac-iter k 0))
  ```

  It takes about 11 steps to get 4 decimal place.

- 1.38

  ```scheme
  (define (d i)
    (cond ((= (remainder i 3) 2) (* 2 (/ (+ 1 i) 3)))
          (else 1)))
  
  (define (n i)
    1)
  ```
  
  ```scheme
  > (+ 2 (cont-frac n d 15))
  2.718281828470584
  ```
  
- 1.39
  
  ```scheme
  (define (tancf x k)
    (define (d i)
      (- (* 2 i) 1))
    (define (n i)
      (if (= i 1) x (-(* x x))))
    (cont-frac n d k))
  ```

### 1.3.4 Procedures as Returned Values

Now we have learnt how to pass procedures as arguments, we can further this idea by allowing procedures as return values.
Remember the `average damping` technique? We want a procedure where given a function $f$, we return the function whose value at $x$ is equal to the average of $x$ and $f(x)$.

```scheme
(define (average-damp f)
  (lambda (x) (average x (f x))))
```

Using this idea we can reformulate the `sqrt` procedure:
```scheme
(define (sqrt x)
  (fixed-point (average-damp (lambda (y) (/ x y)))
               1.0))
```

- Newton's method
  This method states that the zeroes of any differentiable function `g(x)` is the fixed point of the function $x \mapsto f(x)$ where
  $$
  f(x)=x-\frac{g(x)}{g'(x)}
  $$
  To express this idea we first need to define derivative:
  ```scheme
  (define (deriv g)
    (lambda (x)
      (/ (- (g (+ x dx)) (g x))
         dx)))
  (define dx 0.00001)
  ```

  Now
  ```scheme
  (define (newton-transform g)
    (lambda (x)
      (- x (/ (g x) ((deriv g) x)))))
  (define (newtons-method g guess)
    (fixed-point (newton-transform g) guess))
  
  (define (sqrt x)
    (newtons-method (lambda (y) (- (square y) x))
                    1.0))
  ```

- Abstractions and first-class procedures:
  Now we've actually defined `sqrt` using three different implementation of the fixed-point method:
  $y\mapsto x/y$ (note that this doesn't work), the average damp of $y\mapsto x/y$, and the Newton transformation of $y\mapsto y^2 - x$.
  Now we want a general procedure that can express all these methods:

  ```scheme
  (define (fixed-point-of-transform g transform guess)
    (fixed-point (transform g) guess))
  
  (define (sqrt x)
    (fixed-point-of-transform (lambda (y) (/ x y))
                              identity
                              1.0)) ; note that this doesn't work
  
  (define (sqrt x)
    (fixed-point-of-transform (lambda (y) (/ x y))
                              average-damp
                              1.0))
  
  (define (sqrt x)
    (fixed-point-of-transform (lambda (y) (- (square y) x))
                              newton-transform
                              1.0))
  ```

  As programmers, we should always be aware of the possibility of abstracting general methods as higher-order procedures and to use them as fundamental elements.

  In general, programming languages impose restrictions on the ways in which computational elements can be manipulated. Elements with the fewest restrictions are said to have first-class status. Some of the “rights and privileges” of first-class elements are:

  - They may be named by variables.
  - They may be passed as arguments to procedures.
  - They may be returned as the results of procedures.
  - They may be included in data structures.

  Lisp grants procedures full first-class status unlike other common programming languages. This will impose some efficiency problems but it is worth it.

#### Exercise 1.40-1.46

- 1.40

  ```scheme
  (define (cubic a b c)
    (lambda (x) (+ (* x x x) (* a x x) (* b x) c)))
  ```

- 1.41

  ```scheme
  (define (double x)
    (lambda (a) (x (x a))))
  ```

  ```scheme
  > (((double (double double)) inc) 5)
  21
  ```

- 1.42

  ```scheme
  (define (compose f g)
    (lambda (x) (f (g x))))
  ```

- 1.43
    ```scheme
    (define (repeated f n)
        (if (= n 1)
            f
        (lambda (x) (f ((repeated f (- n 1)) x)))))
    ```

      Note how you don't need to write `(lambda (x) (f x))` for the first if-clause. `f` is sufficient.

- 1.44

    ```scheme
    (define (smooth f)
      (lambda (x) (/ (+ (f (+ x dx)) (f x) (f (- x dx))) 3)))
    ```

    ```scheme
    (define (n-time-smooth f n)
      ((repeated smooth n) f))
    ```

- 1.45
    From experiment, we conclude that for calculating the $n$th root we need $\lfloor \lg{n} \rfloor$ (base is 2) times of average damping.

    ```scheme
    (define (lg n)
        (cond ((> (/ n 2) 1)
                (+ 1 (lg (/ n 2))))
              ((< (/ n 2) 1)
                0)
              (else
                1)))
    ```

    Note that the flooring is already done.
    Let's write the n-times average damping procedure:

    ```scheme
    (define (average-damp-n f n)
      (if (= n 1)
          (lambda (x) (/ (+ (f x) x) 2))
          (lambda (x) (/ (+ (average-damp-n f (- n 1)) x) 2))))
    
    (define (nth-root base n)
      (fixed-point (average-damp-n (lambda (x) (/ base (expt x (- n 1)))) (lg n)) 1.5))
    ```

    Note that 1.5 is just a initial guess.

- 1.46

    ```scheme
    (define (iterative-improve enough? improve)
      (lambda (guess)
        (if (enough? guess)
            guess
            ((iterative-improve enough? improve) (improve guess)))))
    ```

    ```scheme
    (define (sqrt x)
        (define dx 0.00001)
        (define (close-enough? guess)
            (< (abs (- (square guess) x)) dx))
        (define (improve guess)
            (average guess (/ x guess)))
        (define (average x y)
            (/ (+ x y) 2))
        ((iterative-improve close-enough? improve) 1.0))
    ```

    ```scheme
    (define (fixed-point f guess)
      (define tolerance 0.0000001)
      (define (enough? x)
        (< (abs (- (f x) x)) tolerance))
      ((iterative-improve enough? f) guess))
    ```

    Note in `fixed-point`, the improve procedure is in fact the `f`.

# 2 Building Abstractions with Data

In this chapter we are going to look at more complex data. Instead of combining procedures to form compound procedures, we now construct compound data objects by combining data objects.

There are many advantages when using compound data. One of them is that it enables *data abstraction*, which is the general technique of isolating the parts of a program that deal with how data objects are represented from the parts that deal with how data object are used.

We will see how data abstraction enables us to erect *abstraction barriers*, and that the key to forming compound data is to provide some kind of "glue".  We will explore the idea of *closure* and *conventional interfaces*, as well as *generic operations* and *data-directed programming*.

## 2.1 Introduction to Data Abstraction

In section 1.1.8 we noted how black-box abstraction works for procedures: we can know nothing about the detail of the implementation of procedures. Same thing works with data—*data abstraction*. The interface between these two parts of the system will be a set of procedures called *selectors* and *constructors*. Let's design a set of procedures that manipulate rational numbers:

### 2.1.1 Example: Arithmetic Operations for Rational Numbers

Let us begin by *wishful thinking* that we already have ways to construct a rational number from a numerator and a denominator (a *constructor*) and given a rational number we have ways to extract its numerator and denominator (a *selector*).

- `(make-rat <n> <d>)`
- `(numer <x>)` and `(denom <x>)`

We don't yet care how these are implemented. Using these we can then add, subtract, multiply, divide and test equality by the following relations:
$$
\frac{n_1}{d_1}+\frac{n_2}{d_2} =& \frac{n_1d_2+n_2d_1}{d_1d_2}\\
\frac{n_1}{d_1}-\frac{n_2}{d_2} =& \frac{n_1d_2-n_2d_1}{d_1d_2}\\
\frac{n_1}{d_1}\times \frac{n_2}{d_2} =& \frac{n_1n_2}{d_1d_2}\\
\frac{n_1/d_1}{n_2/d_2} =& \frac{n_1d_2}{d_1n_2} \\
\frac{n_1}{d_1} =& \frac{n_2}{d_2} \quad \text{if and only if   }n_1d_2=n_2d_1
$$
We can express these rules as procedures:

```scheme
(define (add-rat x y)
  (make-rat (+ (* (numer x) (denom y))
               (* (numer y) (denom x)))
            (* (denom x) (denom y))))

(define (sub-rat x y)
  (make-rat (- (* (numer x) (denom y))
               (* (numer y) (denom x)))
            (* (denom x) (denom y))))

(define (mul-rat x y)
  (make-rat (* (numer x) (numer y))
            (* (denom x) (denom y))))

(define (div-rat x y)
  (make-rat (* (numer x) (denom y))
            (* (denom x) (numer y))))

(define (equal-rat? x y)
  (= (* (numer x) (denom y))
     (* (numer y) (denom x))))
```

Now it's time to define the concrete level of our data abstraction. Lisp provides a compound structure called a *pair*.
To construct a *pair*, use a primitive procedure `cons`. To extract the parts, use the primitive procedures `car` and `cdr`.

```scheme
(define x (cons 1 2))

(car x)
:1
(cdr x)
:2
```

Note that a pair is a data object that can be given a name and manipulate, just like a primitive data object. You can also nest pairs:
```scheme
(define x (cons 1 2))
(define y (cons 3 4))
(define z (cons x y))
(car (car z))
:1
(car (cdr z))
:3
```

We will see in following sections that this `cons` is the only glue we need to create complex data objects. The objects constructed from pairs are called *list-structured* data.

Now we can represent rational numbers using pairs.
```scheme
(define (make-rat n d) (cons n d))
(define (numer x) (car x))
(define (denom x) (cdr x))

; note that you can also use an alternative definition
(define make-rat cons)
(define numer car)
(define denom cdr)

(define (print-rat x)
  (newline)
  (display (numer x))
  (display "/")
  (display (denom x)))
```

Now we also notice that our implementation does not reduce rational numbers to lowest terms. So we should change `make-rat` to: 
```scheme
(define (make-rat n d)
  (let ((g (gcd n d)))
    (cons (/ n g) (/ d g))))
```

Notice how the modification is accomplished only by changing the constructor, without changing any of the procedures that implement the actual operations.

#### Exercise 2.1

- 2.1

  ```scheme
  (define (make-rat n d)
    (let ((g (gcd n d)))
      (if (< d 0)
          (cons (- (/ n g)) (- (/ d g)))
          (cons (/ n g) (/ d g)))))
  ```

### 2.1.2 Abstraction Barriers

- Rational numbers in problem domain

  `add-rat`	`sub-rat`

- Rational numbers as numerators and denominators

  `make-rat`	`numer`	`denom`

- Rational numbers as pairs

  `cons`	`car`	`cdr`

- However pairs are actually implemented

These procedures serve as *abstraction barriers* that isolate different "levels" of the system and also the interface between them. The advantage of doing this is that you can modify any layer without affecting the other layers.

#### Exercise 2.2-2.3

- 2.2

  ```scheme
  (define (make-segment start-point end-point)
    (cons start-point end-point))
  
  (define (start-segment segment)
    (car segment))
  
  (define (end-segment segment)
    (cdr segment))
  
  (define (make-point x y)
    (cons x y))
  
  (define (x-point point)
    (car point))
  
  (define (y-point point)
    (cdr point))
  
  (define (midpoint-segment segment)
    (make-point (/ (+ (x-point (start-segment segment)) (x-point (end-segment segment))) 2) (/ (+ (y-point (start-segment segment)) (y-point (end-segment segment))) 2)))
  ```

- 2.3
  Note that the diagonal of a rectangle fully represents it, so:

  ```scheme
  (define (make-rec start-point end-point)
    (cons start-point end-point))
  
  (define (x-length rec)
    (abs(- (x-point (start-rec rec)) (x-point (end-rec rec)))))
  
  (define (y-length rec)
    (abs(- (y-point (start-rec rec)) (y-point (end-rec rec)))))
  
  (define (start-rec rec)
    (car rec))
  
  (define (end-rec rec)
    (cdr rec))
  
  (define (make-point x y)
    (cons x y))
  
  (define (x-point point)
    (car point))
  
  (define (y-point point)
    (cdr point))
  
  (define (perimeter rec)
    (* 2 (+ (x-length rec) (y-length rec))))
  
  (define (area rec)
    (* (x-length rec) (y-length rec)))
  ```

  Observe how we can change the implementation constructor `make-rec` and the selector `x-length` and `y-length` to anything else without affecting `perimeter` and `area`

### 2.1.3 What Is Meant by Data

In general, we can think of data as defined by some collection of selectors and constructors, together with specified conditions that these procedures must fulfill in order to be a valid representation. For example, for the rational numbers, the only condition is:
$$
\frac{\text{(numer x)}}{\text{(denom x)}}=\frac{n}{d}
$$
Another example is pairs: we never said what pairs actually are, we only introduced how `cons`, `car` and `cdr` work: if you glue together two objects using `cons` you can retrieve them using `car` and `cdr`. In fact, any triple of procedures that satisfy this rule can be used for implementing pairs. A **striking** example of this is that we can implement pairs without using any data structure but simply procedures:
```scheme
(define (cons x y)
  (define (dispatch m)
    (cond ((= m 0) x)
          ((= m 1) y)
          (else (error "Argument not 0 or 1 -- CONS" m))))
  dispatch)

(define (car z) (z 0))
(define (cdr z) (z 1))
```

The subtle point here is that, the value returned by `(cons x y)` is actually a procedure—namely, the internally defined procedure called `dispatch`. This is called the procedural representation of data structures.  The point is, although lisp's pair doesn't work this way (they are primitives and are implemented directly,) **the ability to manipulate procedures as common objects automatically provides the ability to represent compound data.** This style of programming is called *message passing* because the way the selector works is through passing message to the constructor's procedure via arguments.

#### Exercise 2.4-2.6

- 2.4

  ```scheme
  (define (cons x y)
    (lambda (m) (m x y)))
  
  (define (car z)
    (z (lambda (p q) p)))
  
  ;verification
  (car (cons x y))
  (car (lambda (m) (m x y)))
  ((lambda (m) (m x y)) (lambda (p q) p))
  ((lambda (p q) p) x y)
  x
  
  ;definition of cdr
  (define (cdr z)
    (z (lambda (p q) q)))
  ```

  The point of this definition is that the `cons` simply creates an opportunity for a certain procedure to be exerted upon `x` and `y`. The `car` procedure simply feeds the "select the first argument" procedure into `cons` and then `cons` feed this procedure to `x` and `y`.

- 2.5

  ```scheme
  (define (cons x y)
    (* (expt 2 x) (expt 3 y)))
  
  (define (car x)
    (if (= 0 (remainder x 2))
        (+ 1 (car (/ x 2)))
        0))
  
  (define (cdr x)
    (if (= 0 (remainder x 3))
        (+ 1 (car (/ x 3)))
        0))
  ```

- 2.6

  ```scheme
  ;evaluate
  (add-1 zero)
  (add-1 (lambda (f) (lambda (x) x)))
  (lambda (f) (lambda (x) (f ((n f) x)))) ;(n f) is obviously (lambda (x) x)
  (lambda (f) (lambda (x) (f x))); ((lambda (x) x) x) is x
  ;this is one.
  
  (define one
    (lambda (f) (lambda (x) (f x))))
  
  ;evaluate (add-1 one)
  (add-1 one)
  (add-1 (lambda (f) (lambda (x) (f x))))
  (lambda (f) (lambda (x) (f ((n f) x)))); (n f) is obviously (lambda (x) (f x))
  (lambda (f) (lambda (x) (f (f x)))) ;((lambda (x) (f x)) x) is (f x)
  ;this is two
  
  (define two
    (lambda (f) (lambda (x) (f (f x)))))
  ```
  It's easy to see now that numbers here are procedures that eats one procedure as an argument and then return a procedure like `(lambda (x) (f (f (f (f...(f x))))))` where the number is the amount of nested `f`. The `add-1` procedure first feeds the number with a procedure `f`, and then feed the returned procedure with a argument `x`, and then wrap another `f` around the whole thing to add one.
  
  ```scheme
  (define (+ a b)
    (lambda (f) (lambda (x) ((b f) ((a f) x)))))
  ;moreover we can see that
  (define (* a b)
    (lambda (f) (b (a f))))
  
  (define (exp a b)
    (b a))
  ```
  

### 2.1.4 Extended Exercise: Interval Arithmetic

Hacker wants to design a system to help calculate inexact numbers involving an interval.
```scheme
(define (add-interval x y)
  (make-interval (+ (lower-bound x) (lower-bound y))
                 (+ (upper-bound x) (upper-bound y))))

(define (mul-interval x y)
  (let ((p1 (* (lower-bound x) (lower-bound y)))
        (p2 (* (lower-bound x) (upper-bound y)))
        (p3 (* (upper-bound x) (lower-bound y)))
        (p4 (* (upper-bound x) (upper-bound y))))
    (make-interval (min p1 p2 p3 p4)
                   (max p1 p2 p3 p4))))

(define (div-interval x y)
  (mul-interval x 
                (make-interval (/ 1.0 (upper-bound y))
                               (/ 1.0 (lower-bound y)))))
```

#### Exercise 2.7-2.16

- 2.7

  ```scheme
  (define (lower-bound inv)
    (car inv))
  
  (define (upper-bound inv)
    (cdr inv))
  ```

- 2.8

  ```scheme
  (define (sub-interval x y)
    (make-interval (- (lower-bound x) (upper-bound y))
                   (- (upper-bound x) (lower-bound y))))
  ```

  

- 2.9
  For addition/subtraction:
  $$
  width_{end}=max(width_1,width_2)
  $$
  For multiplication, see $(1,2)\times(1,2)=(1,4)$ and $(2,3)\times(2,3)=(4,9)$
  
- 2.10

  ```scheme
  (define (div-interval x y)
    (if (or (and (< (lower-bound x) 0)
                 (> (upper-bound x) 0))
            (and (< (lower-bound y) 0)
                 (> (upper-bound y) 0)))
        (error "divide by zero")
        (mul-interval x 
                  (make-interval (/ 1.0 (upper-bound y))
                                 (/ 1.0 (lower-bound y))))))
  ```

- 2.11

  ```scheme
  (define (mul-interval x y)
    (let ((a (lower-bound x))
          (b (upper-bound x))
          (c (lower-bound y))
          (d (upper-bound y)))
      (cond (((and (< a 0)
                  (< b 0)
                  (< c 0)
                  (< d 0))
             (make-interval (* b d) (* a c)))
             ((and (< a 0)
                  (> b 0)
                  (< c 0)
                  (< d 0))
             (make-interval (* b c) (* a c)))
             ((and (> a 0)
                  (> b 0)
                  (< c 0)
                  (< d 0))
             (make-interval (* b c) (* a d)))
             ((and (< a 0)
                  (< b 0)
                  (< c 0)
                  (> d 0))
             (make-interval (* a d) (* a c)))
             ((and (< a 0)
                  (> b 0)
                  (< c 0)
                  (> d 0))
             (make-interval (min (* a d) (* b c)) (max (* a c) (* b d))))
             ((and (> a 0)
                  (> b 0)
                  (< c 0)
                  (> d 0))
             (make-interval (* b c) (* b d)))
             ((and (< a 0)
                  (< b 0)
                  (> c 0)
                  (> d 0))
             (make-interval (* a d) (* b c)))
             ((and (< a 0)
                  (> b 0)
                  (> c 0)
                  (> d 0))
             (make-interval (* a d) (* b d)))
             ((and (> a 0)
                  (> b 0)
                  (> c 0)
                  (> d 0))
             (make-interval (* a c) (* b d)))))))
  ```

- 2.12

  ```scheme
  (define (make-center-percent c p)
    (make-interval (- c (* c (/ p 100))) (+ c (* c (/ p 100)))))
  
  (define (center i)
    (/ (+ (lower-bound i) (upper-bound i)) 2))
  
  (define (percent i)
    (/ (- (upper-bound i) (lower-bound i)) (* 0.02 (center i))))
  ```

- 2.13
  $$
  (1+x)a\times (1+y)b=(1+x+y+xy)ab
  $$
  for small $x$ and $y$, the new interval is about the product of the two percentage tolerance.

- 2.14
  This is actually a rather trivial question after some thought. If a quantity is occuring more than once in a formula, the interval we are calculating might not be correct because the quantity cannot have more than one value at once albeit its uncertaincy. For example, $A/A$ should have been just one—there is no uncertaincy, but in our program it will become an interval.

- 2.15
  She is right. See above. If all number only occurs once, this will not be a problem at all.

- 2.16
  I would say that this would require a whole algebraic system to complete since for example even to calculate simple formulas like this involves derivatives:
  $$
  x^3-x^2+x, \text{ where x is an interval}
  $$
  This problem is called [*dependency problem*](https://en.wikipedia.org/wiki/Interval_arithmetic#Dependency_problem).

## 2.2 Hierarchical Data and the Closure Property

Remember that we can combine pairs using pairs themselves? 
An operation for combining data objects satisfy the *closure property* if the results of combining things with that operation can themselves be combined using the same operation. It's called *closure* because applying the combination result the same structure.
Closure permits *hierarchical* structures—structures made up of parts, which themselves are made up of parts and so on and on.
From Chapter one we know that procedures themselves have closure property.

### 2.2.1 Representing Sequences

Using pairs we can easily form sequences in many ways. One of them is using a list (a chain list):
```scheme
(cons 1
      (cons 2
            (cons 3
                  (cons 4 nil))))
```

`Scheme` provides a primitive called `list` to help constructing:

```scheme
(list <a1> <a2> ... <an>)
```

Lisp also conventionally prints lists as natural forms like `(1 2 3 4)`. However, `(1 2 3 4)` itself is not a valid expression (`1` is not a procedure) but only a convenient way to print.

You can use `car` and `cdr` repeatedly to access the elements in a list. You can also use things like `cadr`(stands for `(car (cdr list))`) or `caadr` to abbreviate.

The value of `nil` can be thought of as an empty sequence.

The procedure `list-ref` returns the $nth$ item in a list (note that it start with zero)
```scheme
(define (list-ref items n)
  (if (= n 0)
      (car items)
      (list-ref (cdr items) (- n 1))))
```

Procedure `length`:
```scheme
(define (length items)
  (define (length-iter a count)
    (if (null? a)
        count
        (length-iter (cdr a) (+ 1 count))))
  (length-iter items 0))
```

`append` is to concatenate two lists together in that order:

```scheme
(define (append list1 list2)
  (if (null? list1)
      list2
      (cons (car list1) (append (cdr list1) list2))))
```

**One important note** 
Appending single elements into an existing list is pretty hard using only primitive procedures. However, putting the single elements at the top (first) is easy:

````scheme
(cons first-element list)
````

Therefore, always try to avoid append. Try using reverse at the end. Especially if you are using a iterative process.

---

You can use the *dotted-tail notation* if you want to include a list in your procedure's parameters:
```scheme
(define (f x y . z) <body>)
;or
(define f (lambda (x y . z) <body>))
> (f 1 2 3 4 5 6)
x: 1
y: 2
z: (3 4 5 6)

(define (g . w) <body>)
;or
(define g (lambda w <body>)); NOTICE how you don't need the parenthesis when using a single list parameter
> (g 1 2 3 4)
w: (1 2 3 4)
```

#### Exercise 2.17-2.23

- 2.17

  ```scheme
  (define (last-pair x)
    (if (null? (cdr x))
        (car x)
        (last-pair (cdr x))))
  ```

- 2.18

  ```scheme
  (define (reverse l)
  (define (iter-reverse x acc)
    (if (null? x)
        acc
        (iter-reverse (cdr x) (cons (car x) acc))))
    (iter-reverse l '()))
  ```

- 2.19

  ```scheme
  (define (cc amount coin-values)
    (define no-more? null?)
    (define except-first-denomination cdr)
    (define first-denomination car)
    (cond ((= amount 0) 1)
          ((or (< amount 0) (no-more? coin-values)) 0)
          (else
           (+ (cc amount
                  (except-first-denomination coin-values))
              (cc (- amount
                     (first-denomination coin-values))
                  coin-values)))))
  ```

  No it doesn't affect the answer. Trivial due to symmetry.

- 2.20

  ```scheme
  (define (same-parity x . z)
    (define (iter-par par acc rem)
      (if (null? rem)
          acc
          (if (= par (remainder (car rem) 2))
              (iter-par par (cons (car rem) acc) (cdr rem))
              (iter-par par acc (cdr rem)))))
    (reverse (iter-par (remainder x 2) (cons x '()) z))) ; note how you need a nil nested deep inside
  ```

- **Mapping over lists:**
  Sometimes you want to apply a procedure to all the elements in a list. Lisp contains a primitive procedure called `map` that can achieve this:

  ```scheme
  (define (map proc items)
    (if (null? items)
        nil
        (cons (proc (car items))
              (map proc (cdr items)))))
  ```

  You can also provide a procedure with $n$ arguments, and then feed it with $n$ lists.
  ```scheme
  (map + (list 1 2 3) (list 40 50 60) (list 700 800 900))
  : (741 852 963)
  ```

  `maps` provides a higher level of abstraction: it establishes an abstraction barrier that isolates the implementation of procedures that transform lists from the details of how the elements of the list are extracted and combined.

- 2.21

  ```scheme
  (define (square-list items)
    (if (null? items)
        nil
        (cons (square (car items))
              (square-list (cdr items)))))
  
  (define (square-list items)
    (map square items))
  ```

- 2.22
  This is exactly the thing about appending list: in an iterative process you almost always want to append elements into the `accmulator` list. But that is hard to do. The best is to use `reverse`.

  ```scheme
  (define (square-list items)
    (define (iter things answer)
      (if (null? things)
          (reverse answer) ;reverse here
          (iter (cdr things) 
                (cons (square (car things))
                      answer))))
    (iter items nil))
  ```

- 2.23

  ```scheme
  (define (for-each proc list)
    (cond ((null? list))
          (else (proc (car list)) (for-each proc (cdr list)))))
  ```

### 2.2.2 Hierarchical Structures

We can often regard complex data objects like `((1 2) 3 4)` as trees. (note how: this is constructed by `(cons (list 1 2) (list 3 4))`!!!! the `cdr` of this object is `(3 4)`!) Recursion is a natural tool when you are trying to do something to the trees because of its closure property.
We want to implement a `count-leaves` procedure:

```scheme
(define (count-leaves x)
  (cond ((null? x) 0)
        ((not (pair? x)) 1)
        (else (+ (count-leaves (car x))
                 (count-leaves (cdr x))))))
```

A little tip about recursively visiting every leaves on a tree:
```scheme
(cons (proc (car tree))
      (proc (cdr tree)))
```

That'll do.

#### Exercise 2.24-32

- 2.24

  ```scheme
  (1 (2 (3 4)))
  ```

- 2.25

  ```scheme
  (1 3 (5 7) 9)
  : cadaddr ;important!
  ((7))
  : caar ;note how this is actually (cons (cons 7 nil) nil)
  (1 (2 (3 (4 (5 (6 7))))))
  : cadadadadadadad
  ```

  Always note that **`cdr` always gives a list!!!!** `(cdr (list 5 7))` is `(7)`(i.e. `(cons 7 nil)`) not `7`!!! `(cdr (1 (2 (3 (4 (5 (6 7)))))))` is `((2 (3 (4 (5 (6 7))))))`!!!

- 2.26
  
  ```scheme
  > (append x y)
  (1 2 3 4 5 6)
  
  > (cons x y)
  ((1 2 3) 4 5 6)
  
  > (list x y)
  ((1 2 3) (4 5 6))
  ```
  
- 2.27

  ```scheme
  (define (deep-reverse l)
    (define (dreverse x acc)
      (cond ((null? x)
           acc)
          ((pair? (car x))
           (dreverse (cdr x) (cons (dreverse (car x) '()) acc)))
          (else
           (dreverse (cdr x) (cons (car x) acc)))))
    (dreverse l '()))
  ```

- 2.28
  **Note that**, when we talk about trees, we do not consider the `nil` or `'()` part of the list as a general rule.

  ```scheme
  (define (fringe tree)
    (cond ((null? tree)
           '())
          ((not (pair? tree))
           (list tree))
          (else
           (append (fringe (car tree)) (fringe (cdr tree))))))
  ```

  **traversing binary tree**:

  - If empty tree, empty list
  - If leave, return a list with only leave
  - Else, **append** the left sub-tree `(car tree)` and the right sub-tree `(cdr tree)`

- 2.29
  a.

  ```scheme
  (define left-branch car)
  (define right-branch cadr)
  
  (define branch-length car)
  (define branch-structure cadr)
  ```

  b.
  ```scheme
  (define (total-weight node)
    (if (not (pair? node))
        node
        (+ (branch-structure (left-branch node)) (branch-structure (right-branch node)))))
  ```

  c.
  ```scheme
  (define (balanced? mobile)
    (cond ((not (pair? mobile))
           #t)
          ((= (* (branch-length (left-branch mobile)) (total-weight (branch-structure (left-branch mobile)))) (* (branch-length (right-branch mobile)) (total-weight (branch-structure (right-branch mobile)))))
           (and (balanced? (branch-structure (left-branch mobile))) (balanced? (branch-structure (right-branch mobile)))))
          (else
           #f)))
  ```

  d.

  ```scheme
  (define left-branch car)
  (define right-branch cdr)
  
  (define branch-length car)
  (define branch-structure cdr)
  ```

  That'll do. We have built an *abstraction barrier*.

- **Mapping over trees**
  You can also `map` over trees using simple recursion:

  ```scheme
  (define (scale-tree tree factor)
    (cond ((null? tree) nil)
          ((not (pair? tree)) (* tree factor))
          (else (cons (scale-tree (car tree) factor)
                      (scale-tree (cdr tree) factor)))))
  ;alternatively
  
  (define (scale-tree tree factor)
    (map (lambda (sub-tree)
           (if (pair? sub-tree)
               (scale-tree sub-tree factor)
               (* sub-tree factor)))
         tree))
  ```

  Note how you can 

- 2.30

  ```scheme
  (define (square-tree tree)
    (cond ((null? tree) nil)
          ((not (pair? tree)) (* tree tree))
          (else (cons (square-tree (car tree))
                      (square-tree (cdr tree))))))
  
  (define (square-tree tree)
    (map (lambda (sub-tree)
           (if (pair? sub-tree)
               (square-tree sub-tree)
               (* sub-tree sub-tree)))
         tree))
  ```

  Note the usage of `map` in the second procedure.

- 2.31

  ```scheme
  (define (tree-map proc tree)
    (map (lambda (sub-tree)
           (if (pair? sub-tree)
               (tree-map proc sub-tree)
               (proc sub-tree)))
         tree))
  ```

- 2.32

  ```scheme
  (define (subsets s)
    (if (null? s)
        (list nil)
        (let ((rest (subsets (cdr s))))
          (append rest (map (lambda (x)
                              (cons (car s) x)) rest)))))
  ```

  When you add a new element into a set, every subset split into two with one without the new element and one with the new element. Build up the set backwards and you'll get this procedure.

### 2.2.3 Sequences as Conventional Interfaces

In this section, we introduce another powerful design principle for working with data structures: *conventional interfaces.* What's that? Imagine a streamline where products are rolling. The sequence would be the product with the streamline being the *signal-flow* structure in this analogy.

Let's revisit our `sum-odd-squares` procedure:
```scheme
(define (sum-odd-squares tree)
  (cond ((null? tree) 0)
        ((not (pair? tree))
         (if (odd? tree) (square tree) 0))
        (else (+ (sum-odd-squares (car tree))
                 (sum-odd-squares (cdr tree))))))
```

This looks very different from this following procedure which constructs a list of all the even Fibonacci numbers:
```scheme
(define (even-fibs n)
  (define (next k)
    (if (> k n)
        nil
        (let ((f (fib k)))
          (if (even? f)
              (cons f (next (+ k 1)))
              (next (+ k 1))))))
  (next 0))
```

However, they are actually similar because the first program:

- enumerates the leaves of a tree
- filters them, selecting the odd ones
- squares each of the selected ones (mapping)
- accumulate the result using +, starting with 0

The second program:

- enumerates the integers from 0 to n
- computes the Fibonacci number for each integer (mapping)
- filters them, selecting the even ones
- accumulate the results using cons, starting with an empty list

The way we conceptualize these steps are called signal-flow structure.
However, in both procedures, the signal flow structure is unclear and lack modularity. We want to change that and organize our programs to make the signal-flow structure manifest in the procedures we write.

The key to organize programs this way is to concentrate on the **signals** that flow from one stage in the process to the next. We can represent these signals as lists and use list operations to process the signals.

For instance, for mapping we can simply use the `map` procedure
```scheme
(map square (list 1 2 3 4 5))
```

For filtering we can:
```scheme
(define (filter predicate sequence)
  (cond ((null? sequence) nil)
        ((predicate (car sequence))
         (cons (car sequence)
               (filter predicate (cdr sequence))))
        (else (filter predicate (cdr sequence)))))
```

For accumulating:
```scheme
(define (accumulate op initial sequence)
  (if (null? sequence)
      initial
      (op (car sequence)
          (accumulate op initial (cdr sequence)))))
```

For enumerating (either an interval of a tree) (for trees this is exactly the `fringe` procedure)
```scheme
(define (enumerate-interval low high)
  (if (> low high)
      nil
      (cons low (enumerate-interval (+ low 1) high))))


(define (enumerate-tree tree)
  (cond ((null? tree) nil)
        ((not (pair? tree)) (list tree))
        (else (append (enumerate-tree (car tree))
                      (enumerate-tree (cdr tree))))))
```

Now, we can reformulate the procedures:
```scheme
(define (sum-odd-squares tree)
  (accumulate +
              0
              (map square
                   (filter odd?
                           (enumerate-tree tree)))))

(define (even-fibs n)
  (accumulate cons
              nil
              (filter even?
                      (map fib
                           (enumerate-interval 0 n)))))
```

This way, we could encourage modular design by providing a library of standard components, together with a *conventional interface* (i.e. the sequence and its operations) for connecting the components in flexible ways.

When we universally represent structures as sequences (using `enumerators`) we have localized the data-structure dependecies to a small number of sequence operations, and these operations can be later on changed while leaving the overall design of our programs intact.

#### Exercise 2.33-2.43

- 2.33
  To do that, recall the definition of accumulate:

  ```scheme
  (define (accumulate op initial sequence)
    (if (null? sequence)
        initial
        (op (car sequence)
            (accumulate op initial (cdr sequence)))))
  ```

  ```scheme
  (define (map p sequence)
    (accumulate (lambda (x y) (cons (p x) y) nil sequence)))
  
  (define (append seq1 seq2)
    (accumulate cons seq2 seq1))
  
  (define (length sequence)
    (accumulate (lambda (x y) (+ 1 y)) 0 sequence))
  ```

- 2.34

