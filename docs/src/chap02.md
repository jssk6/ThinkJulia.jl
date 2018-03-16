# Variables, expressions and statements

One of the most powerful features of a programming language is the ability to manipulate **variables**. A variable is a name that refers to a value.

## Assignment statements

An **assignment statement** creates a new variable and gives it a value:

```@repl chap02
message = "And now for something completely different"
n = 17
π = 3.141592653589793
```

This example makes three assignments. The first assigns a string to a new variable named `message`; the second gives the integer `17` to `n`; the third assigns the (approximate) value of ``\pi`` to `π`.

A common way to represent variables on paper is to write the name with an arrow pointing to its value. This kind of figure is called a **state diagram** because it shows what state each of the variables is in (think of it as the variable's state of mind). Figure 1 shows the result of the previous example.

```@eval
using TikzPictures
p = TikzPicture(L"""
\node[draw, fill=lightgray, minimum width=10cm, minimum height=1.5cm]{};
\node[anchor=east] (me) at(-3.5, 0.5) {\tt message};
\node[anchor=west] (mev) at (-2.5, 0.5) {\tt "And now for something completely different"};
\draw[-latex] (me) -- (mev);
\node[anchor=east] (n) at(-3.5, 0) {\tt n};
\node[anchor=west] (nv) at (-2.5, 0) {\tt 17};
\draw[-latex] (n) -- (nv);
\node[anchor=east] (pi) at(-3.5, -0.5) {\tt π};
\node[anchor=west] (piv) at (-2.5, -0.5) {\tt 3.141592653589793};
\draw[-latex] (pi) -- (piv);
"""; options="", preamble="""
  \\usepackage{fontspec}
  \\setmonofont[Scale=MatchLowercase]{Ubuntu Mono}
""")
 save(SVG("fig21"), p)
 save(PDF("fig21"), p)
 nothing
```

```@raw html
<figure>
  <img src="fig21.svg" alt="State diagram.">
  <figcaption>Figure 1. State diagram.</figcaption>
</figure>
```

```@raw latex
\begin{figure}
\centering
\includegraphics{fig21}
\caption{State diagram.}
\label{fig21}
\end{figure}
```

## Variable names

Programmers generally choose names for their variables that are meaningful--they document what the variable is used for.

Variable names can be as long as you like. They can contain almost all Unicode characters, but they can't begin with a number. It is legal to use uppercase letters, but it is conventional to use only lower case for variables names.

The underscore character, `_`, can appear in a name. It is often used in names with multiple words, such as `your_name` or `airspeed_of_unladen_swallow`.

If you give a variable an illegal name, you get a syntax error:

```julia
julia> 76trombones = "big parade"
ERROR: syntax: "76" is not a valid function argument name

julia> more@ = 1000000
ERROR: syntax: unexpected "="

julia> type = "Advanced Theoretical Zymurgy"
ERROR: syntax: unexpected "="
```

`76trombones` is illegal because it begins with a number. `more@` is illegal because it contains an illegal character, `@`. But what's wrong with `type`?

It turns out that `type` is one of Julia's **keywords**. The REPL uses keywords to recognize the structure of the program, and they cannot be used as variable names.

Julia has these keywords:

```
abstract    baremodule   begin      break       catch
ccall       const        continue   do          else
elseif      end          export     finally     for
function    global       if         import      importall
let         local        macro      module      mutable
primitive   quote        return     try         type
using       struct       while
```

You don't have to memorize this list. In most development environments, keywords are displayed in a different color; if you try to use one as a variable name, you'll know.

## Expressions and statements

An **expression** is a combination of values, variables, and operators. A value all by itself is considered an expression, and so is a variable, so the following are all legal expressions:

```@repl chap02
42
n
n + 25
```

When you type an expression at the prompt, the REPL **evaluates** it, which means that it finds the value of the expression. In this example, `n` has the value 17 and `n + 25` has the value 42.

A **statement** is a unit of code that has an effect, like creating a variable or displaying a value. 

```@repl
n = 17
println(n)
```

The first line is an assignment statement that gives a value to `n`. The second line is a print statement that displays the value of `n`.

When you type a statement, the REPL **executes** it, which means that it does whatever the statement says. In general, statements don't have values.

## Script mode

So far we have run Julia in **interactive mode**, which means that you interact directly with the REPL. Interactive mode is a good way to get started, but if you are working with more than a few lines of code, it can be clumsy.

The alternative is to save code in a file called a **script** and then run the REPL in **script mode** to execute the script. By
convention, Julia scripts have names that end with `.jl`.

If you know how to create and run a script on your computer, you are ready to go. Otherwise I recommend using JuliaBox again. Open a text file, write the script and save with a `.jl` extension. The script can be executed in a terminal with the command `julia name_of_the_script.jl`.

Because Julia provides both modes, you can test bits of code in interactive mode before you put them in a script. But there are differences between interactive mode and script mode that can be confusing.

For example, if you are using Julia as a calculator, you might type

```@repl
miles = 26.2
miles * 1.61
```

The first line assigns a value to `miles`, but it has no visible effect. The second line is an expression, so the REPL evaluates it and displays the result. It turns out that a marathon is about 42 kilometers.

But if you type the same code into a script and run it, you get no output at all. In script mode an expression, all by itself, has no visible effect. Julia actually evaluates the expression, but it doesn't display the value unless you tell it to:

```@example
miles = 26.2
println(miles * 1.61)
```

This behavior can be confusing at first.

A script usually contains a sequence of statements. If there is more than one statement, the results appear one at a time as the statements execute.

For example, the following script produces the output

```@example
println(1)
x = 2
println(x)
```

The assignment statement produces no output.

To check your understanding, type the following statements in the Julia REPL and see what they do:

```julia
5
x = 5
x + 1
```

Now put the same statements in a script and run it. What is the output? Modify the script by transforming each expression into a print statement and then run it again.

## Order of operations

When an expression contains more than one operator, the order of evaluation depends on the **order of operations**. For mathematical operators, Julia follows mathematical convention. The acronym **PEMDAS** is a useful way to remember the rules:

- **P**arentheses have the highest precedence and can be used to force an expression to evaluate in the order you want. Since expressions in parentheses are evaluated first, `2 * (3-1)` is 4, and `(1+1)^(5-2)` is 8. You can also use parentheses to make an expression easier to read, as in `(minute * 100) / 60`, even if it doesn't change the result.

- **E**xponentiation has the next highest precedence, so `1+2^3` is 9, not 27, and `2*3^2` is 18, not 36.

- **M**ultiplication and **D**ivision have higher precedence than **A**ddition and **S**ubtraction. So `2*3-1` is 5, not 4, and `6+4/2` is 8, not 5.

- Operators with the same precedence are evaluated from left to right (except exponentiation). So in the expression `degrees / 2 * π`, the division happens first and the result is multiplied by `π`. To divide by ``2\pi``, you can use parentheses or write `degrees / 2 / π`.

I don't work very hard to remember the precedence of operators. If I can't tell by looking at the expression, I use parentheses to make it obvious.

Unicode characters that can be entered via tab completion of LaTeX-like abbreviations in the Julia REPL.

## String operations

In general, you can't perform mathematical operations on strings, even if the strings look like numbers, so the following are illegal:

```julia
"2" - "1"    "eggs" / "easy"    "third" + "a charm"
```

But there are two exceptions, `*` and `^`.

The `*` operator performs **string concatenation**, which means it joins the strings by linking them end-to-end. For example:
```@repl
first = "throat"
second = "warbler"
first * second
```

The `^` operator also works on strings; it performs repetition. For example, `"Spam"^3` is `"SpamSpamSpam"`. If one of the values is a string, the other has to be an integer.

This use of `*` and `^` makes sense by analogy with multiplication and exponentiation. Just as `4^3` is equivalent to `4*4*4`, we expect `"Spam"^3` to be the same as `"Spam"*"Spam"*"Spam"`, and it is.

## Comments

As programs get bigger and more complicated, they get more difficult to read. Formal languages are dense, and it is often difficult to look at a piece of code and figure out what it is doing, or why.

For this reason, it is a good idea to add notes to your programs to explain in natural language what the program is doing. These notes are called **comments**, and they start with the `#` symbol:

```julia
# compute the percentage of the hour that has elapsed
percentage = (minute * 100) / 60
```

In this case, the comment appears on a line by itself. You can also put comments at the end of a line:

```julia
percentage = (minute * 100) / 60   # percentage of an hour
```

Everything from the `#` to the end of the line is ignored--it has no effect on the execution of the program.

Comments are most useful when they document non-obvious features of the code. It is reasonable to assume that the reader can figure out *what* the code does; it is more useful to explain *why*.

This comment is redundant with the code and useless:

```julia
v = 5   # assign 5 to v
```

This comment contains useful information that is not in the code:

```julia
v = 5   # velocity in meters/second. 
```

Good variable names can reduce the need for comments, but long names can make complex expressions hard to read, so there is a tradeoff.

## Debugging

Three kinds of errors can occur in a program: syntax errors, runtime errors, and semantic errors. It is useful to distinguish between them in order to track them down more quickly.

- **Syntax error**: "Syntax" refers to the structure of a program and the rules about that structure. For example, parentheses have to come in matching pairs, so `(1 + 2)` is legal, but `8)` is a syntax error.

  If there is a syntax error anywhere in your program, Julia displays an error message and quits, and you will not be able to run the program. During the first few weeks of your programming career, you might spend a lot of time tracking down syntax errors. As you gain experience, you will make fewer errors and find them faster.

- **Runtime error**: The second type of error is a runtime error, so called because the error does not appear until after the program has started running. These errors are also called **exceptions** because they usually indicate that something exceptional (and bad) has happened.

  Runtime errors are rare in the simple programs you will see in the first few chapters, so it might be a while before you encounter one.

- **Semantic error**: The third type of error is "semantic", which means related to meaning. If there is a semantic error in your program, it will run without generating error messages, but it will not do the right thing. It will do something else. Specifically, it will do what you told it to do.

  Identifying semantic errors can be tricky because it requires you to work backward by looking at the output of the program and trying to figure out what it is doing.

## Glossary

*variable*: A name that refers to a value.

*assignment*: A statement that assigns a value to a variable.

*state diagram*: A graphical representation of a set of variables and the values they refer to.

*keyword*: A reserved word that is used to parse a program; you cannot use keywords like `if`, `function`, and `while` as variable names.

*operand*: One of the values on which an operator operates.

*expression*: A combination of variables, operators, and values that represents a single result.

*evaluate*: To simplify an expression by performing the operations in order to yield a single value.

*statement*: A section of code that represents a command or action. So far, the statements we have seen are assignments and print statements.

*execute*: To run a statement and do what it says.

*interactive mode*: A way of using the Julia REPL by typing code at the prompt.

*script mode*: A way of using the Julia REPL to read code from a script and run it.

*script*: A program stored in a file.

*order of operations*: Rules governing the order in which expressions involving multiple operators and operands are evaluated.

*concatenate*: To join two operands end-to-end.

*comment*: Information in a program that is meant for other programmers (or anyone reading the source code) and has no effect on the execution of the program.

*syntax error*: An error in a program that makes it impossible to parse (and therefore impossible to interpret).

*exception*: An error that is detected while the program is running.

*semantics*: The meaning of a program.

*semantic error*: An error in a program that makes it do something other than what the programmer intended.

## Exercises

### Exercise 1

Repeating my advice from the previous chapter, whenever you learn a new feature, you should try it out in interactive mode and make errors on purpose to see what goes wrong.

1. We've seen that `n = 42` is legal. What about `42 = n`?

2. How about `x = y = 1`?

3. In some languages every statement ends with a semi-colon, `;`. What happens if you put a semi-colon at the end of a Julia statement?

4. What if you put a period at the end of a statement?

5. In math notation you can multiply `x` and `y` like this: `x y`. What happens if you try that in Julia?

### Exercise 2

Practice using the Julia REPL as a calculator: 

1. The volume of a sphere with radius ``r`` is ``\frac{4}{3} \pi r^3``. What is the volume of a sphere with radius 5?

2. Suppose the cover price of a book is € 24.95, but bookstores get a 40 % discount. Shipping costs € 3 for the first copy and 75 cents for each additional copy. What is the total wholesale cost for 60 copies?

3. If I leave my house at 6:52 am and run 1 mile at an easy pace (8:15 per mile), then 3 miles at tempo (7:12 per mile) and 1 mile at easy pace again, what time do I get home for breakfast?