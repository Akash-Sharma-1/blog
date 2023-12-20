---
title: Understanding Cyclomatic Complexity - is it really worth it ? 
tags: [Software Architecture]
date: 2023-12-17
---
> Cyclomatic Complexity serves as a fundamental metric of code complexity and has well known usage in software testing and analyis. While it's built on a sound and interesting graph-theory concept, it has fair share of problems associated with it.

# What is Cyclomatic Complexity ?


## Definition

Cyclomatic complexity represents the number of linearly independent paths possible in the code. In other words, it symbolises the number of branching existing in the code.

The intent of this number is to capture the amount of decision logic being performed in the concerned piece of code.


## A bit of a history ...

Interestingly, this metric was introduced by _Thomas McCabe_ in 1976 at a theoretical level aiming to measure the structured complexity of programs in a quantitative way during that time. However, even though it might seem that this metric has an old history , it still is relevant and heavily influential in today's software discussions.


## What is it computed for ?

The original publication by McCabe, referred to the concept of cyclomatic complexity to be computed for a '_module_'. It could resemble to a function or a method in modern programming languages. I'll talk about how a granular level complexity check is beneficial. 

```
Will be referring Cyclomatic Complexity as "CC" from now on 
FYI, i like abbreviations :) 
```

## How is it computed ?

Cyclomatic complexity is calculated through the program's **control-flow graph**, where nodes represent cohesive sets of commands (called as a block) , and directed edges link nodes if the execution of the second command is a potential immediate follow-up to the first one. 

Here, a conditional or loop or similar statements (represented as nodes) would have multiple branching (multiple out-edges) which is used in computation the CC number.
  
![PHTO](https://dz2cdn1.dzone.com/storage/temp/14000108-untitled.jpg)


# Mathematical Foundation

## Graph Theory 

Cyclomatic number is a value in graph theory that tells you the number of the edges required to be remove and break all cycles of the graph i.e the number of independent paths in a graph. Note that its only valid for  for **strongly connected graphs**.

It is also known as a 'circuit rank' and  has quite diverse use cases beyond this software analysis.

The cyclomatic number can be derived by :

$$rank = E - N + C$$

Here, E represents the number of edges, N represents the number of nodes and C represents the connected components.

The derivation of this formula is based on the manipulation of famous Kirchhoff's Law from the Linear Algebra.



## McCabe's derivation

  
Program control flow graphs do not inherently exhibit strong connectivity. However, the introduction of a "virtual edge" that connects the exit node to the entry node transforms them into strongly connected graphs. 

The definition of cyclomatic complexity for program control flow graphs is derived from the cyclomatic number formula by a straightforward addition of one, signifying the impact of the virtual edge. 

This interpretation aligns cyclomatic complexity with the count of independent paths within the standard control flow graph model, elegantly sidestepping the need for explicit reference to the virtual edge.

Since for CFGs, the connected component is 1 as we are considering a single function or method.

This gives us : 

$$cc = E - N + 1$$
  
Taking into consideration the virtual edge - which inevidently contributes to the branching count , the formula becomes : 

$$cc = E - N + 2$$

McCabe proposed a generic formula for "P" number of program exits (was common in the older era program with goto statements)

$$cc = E - N + 2P$$



## Intuitive understanding

Let's break up the CC formula to 3 segments : 

$$cc = E - N + 2$$

$$cc = (E - N) + (1) + (1)$$

- `E-N` would nullify all the nodes with 1 branch and will render us with the count of extra branches of the nodes,
- `1` is added for considering the default path (no branch diversion)
- `1` is accounting for the 'virtual edge' added for the mathematical convenience 



# Examples of some code snippets and associated CC

I have included some function snippets below.
Guess the complexity of these functions and verify them below.
```cpp

void foo1( int a ) {
  if ( a )
     return ;
  else
     return ;
}

void foo2( int a ) {
  if ( a )
     return ;
}

void foo3( int a, int b ) {
  if ( a && b )
     return ;
}

void foo4( int a, int b ) {
  if ( a )
     return ;
  if ( b )
     return ;
}

void foo5( int a, int b ) {
  if ( a )
     if ( b )
        return ;
}

void foo6( int a ) {
  while ( a )
     a-- ;
}

void foo7( int a ) {
  do
     a-- ;
  while ( a );
}


void foo8( int a ) {
  for ( int x = 0; x < a; x++ )
     foo() ;
}

void foo9( int a ) {
  switch ( a )
  {
    case 1:
     return ;
  }
}

void foo10( int a ) {
  switch ( a )
  {
    case 1:
    case 2:
     return ;
  }
}


```
  
  | Function name  | Cyclomatic Complexity  |
  | ----- | --- |
  | foo1  | 2   |
  | foo2  | 2    |
  | foo3  | 2    |
  | foo4  | 3    |
  | foo5  | 3    |
  | foo6  | 2    |
  | foo7  | 2    |
  | foo8  | 2    |
  | foo9  | 2    |
  | foo10 | 3    |

  

#  How does it help in software quality ? 


There are many good reasons to limit cyclomatic complexity. Overly complex modules are more prone to error, are harder to understand, are harder to test, and are harder to modify. Deliberately limiting complexity at all stages of software development, for example as a departmental standard, helps avoid the pitfalls associated with high complexity software. Many organizations have successfully implemented complexity limits as part of their software programs. The precise number to use as a limit, however, remains somewhat controversial. The original limit of 10 as proposed by McCabe has significant supporting evidence, but limits as high as 15 have been used successfully as well. Limits over 10 should be reserved for projects that have several operational advantages over typical projects, for example experienced staff, formal design, a modern programming language, structured programming, code walkthroughs, and a comprehensive test plan. In other words, an organization can pick a complexity limit greater than 10, but only if it is sure it knows what it is doing and is willing to devote the additional testing effort required by more complex modules.

-Reason 1 – It tells us the risk that a function contains undiscovered bugs.


Reason 2 – It tells us the risk that modifying a function will inject a bug
- or the difficulty of the addition of the 



- mdoularity - short code
https://www.math.unipd.it/~tullio/IS-1/2004/Approfondimenti/cyclomatic.htm#:~:text=The%20cyclomatic%20number%20in%20graph%20theory%20is%20defined%20as%20e,node%20to%20the%20entry%20node.

# Interesting use case that I worked for 

- white box testing
- NETWORK ROUTING MECHANISMs

# How to use it with tools ?

There are multiple category of tools that do the CC checks for you.
Many these tools easily get integrated with your CI/CD pipeline.

-  IDE integrated CC checkers : 
	- [Visual Studio*](https://visualstudio.microsoft.com/downloads/ "Go to Visual Studio on visualstudio.microsoft.com") built-in functionality for cyclomatic complexity out-of-the-box.
	- [Visual Studio Code](https://code.visualstudio.com/ "Go to Visual Studio Code on code.visualstudio.com") Various extensions are available like [CodeMetrics](https://marketplace.visualstudio.com/items?itemName=EF8980CD-E5F2-4113-92AE-5789026D677C.MetricsAnalyzer "Go to CodeMetrics on marketplace.visualstudio.com").
	- [JetBrains ReSharper](https://www.jetbrains.com/resharper/ "Go to Resharper on jetbrains.com")
	- [CodeRush](https://www.devexpress.com/products/coderush/ "Go to CodeRush on devexpress.com")
	- [Eclipse Metric Plugin ](https://marketplace.eclipse.org/content/codemr-static-code-analyser)
- Linter based checkers : 
	- ESLint
	- JSHint
	- PyLint
- The OGs :
	- [McCabe IQ](https://www.mccabe.com/)
	- [SonarQube](https://www.sonarqube.org/)

  




# Breaking the system

Special attention here 
- Boolean long expression
	- - multiple operators in a signle if
	- https://teamscale.com/blog/en/news/blog/mccabe-cyclomatic-complexity
- Exemptions of switch case
	- https://stackoverflow.com/questions/30240236/cyclomatic-complexity-of-switch-case-statement
-  Inline Conditional (or [Ternary operator](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/conditional-operator "Go to Conditional Operators on docs.microsoft.com"))

https://www.danylkoweb.com/Blog/hiding-cyclomatic-complexity-SR

- OOps
	- polymorphism
	- https://adavis.info/2015/07/conquering-cyclomatic-complexity.html

# Cons
- Vague idea in original paper
	- different tools have diff implement
- doesnt have the entire picture ? 
	- external functions, API 
- cheating ? 
	- https://softwareengineering.stackexchange.com/questions/344358/cyclomatic-complexity-spread-over-non-reusable-functions
	- advocating function based cyclomatic ?
	- hide spagetti code

# Alternatives
- Cognitive complexity
	- https://www.sonarsource.com/blog/cognitive-complexity-because-testability-understandability/
- MISRA
- https://brainhub.eu/library/measuring-cyclomatic-complexity




# Personal Opinions

It is a good metric to start evaluating if not already. I consider it more of a sniff test than anything to rigidly adhere to. Usually if my function's cyclomatic complexity is too high, I know it with or without a sniffer telling me so.

However, the things I think we should be careful about is to not exploit the metric so as to dilute it's intention.

Donald Knuth rightly mentioned the Goodhart's Law in one of his books. It states that 
```markdown
"A metric that becomes a target ceases to be a good metric."
```

What this really means is that with growing dependence on the metrics, the developers might start to optimize the metric itself and loose the sense of real motive behind the target.


# References 
- mccabe paper


---