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

The most intuitive and what lisp uses is *applicative-order evaluation*, where all the operands are fully evaluated to primitive objects before substituting the formal parameters in the procedure. Another type of evaluation strategy is called *normal-order evaluation* (there are more) where you do not evaluate any of the arguments until they are needed in the body of the function (or obtained an expression involving only primitive operators).

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

#### Exercise

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

#### Exercise

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

### Procedures as Black-Box Abstractions

In our `sqrt` process, we decomposed the procedure into separate parts—where each of the sub-procedures can be deemed as black-box abstractions when called by another sub-procedure.
A user should not need to know how the procedure is implemented in order to use it.

