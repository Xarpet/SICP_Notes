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

Clearly space and space is both $\Theta(n)$. The iterative version of this

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
((lambda (x y z) (+ x y (square z))) 1 2 3)
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
((lambda) (<var1> ... <varn>)
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

However there are subtleties that we can yet understand. Note that internal definition of procedures is always fine.

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

  Lisp grant procedures full first-class status unlike other common programming languages. This will impose some efficiency problems but it is worth it.

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

