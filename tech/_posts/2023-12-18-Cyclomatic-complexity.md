---
title: Understanding Cyclomatic Complexity - is it really worth it ? 
tags: [Software Architecture]
date: 2023-12-18
---
> Cyclomatic complexity is a well known concept

## What is Cyclomatic Complexity ? 

Definition 
to measure the number of linearly independent paths in that function. Stated another way, it tells us how many branches there are in each function


Just to make sure we are talking about the same thing, [Cyclomatic Complexity](http://en.wikipedia.org/wiki/Cyclomatic_complexity) is a metric that was introduced by Thomas McCabe in 1976 and aims at capturing the _complexity_ of a method in a single number. Actually, the original publication talks about the more general term _module_, but today this typically means function or method for most languages.




## Theory
- Original paper - points 

formula understnading and explanation
- virtual edge

## Examples 







##  How does it help in software quality ? 


There are many good reasons to limit cyclomatic complexity. Overly complex modules are more prone to error, are harder to understand, are harder to test, and are harder to modify. Deliberately limiting complexity at all stages of software development, for example as a departmental standard, helps avoid the pitfalls associated with high complexity software. Many organizations have successfully implemented complexity limits as part of their software programs. The precise number to use as a limit, however, remains somewhat controversial. The original limit of 10 as proposed by McCabe has significant supporting evidence, but limits as high as 15 have been used successfully as well. Limits over 10 should be reserved for projects that have several operational advantages over typical projects, for example experienced staff, formal design, a modern programming language, structured programming, code walkthroughs, and a comprehensive test plan. In other words, an organization can pick a complexity limit greater than 10, but only if it is sure it knows what it is doing and is willing to devote the additional testing effort required by more complex modules.

-Reason 1 – It tells us the risk that a function contains undiscovered bugs.


Reason 2 – It tells us the risk that modifying a function will inject a bug
- or the difficulty of the addition of the 



- mdoularity - short code
https://www.math.unipd.it/~tullio/IS-1/2004/Approfondimenti/cyclomatic.htm#:~:text=The%20cyclomatic%20number%20in%20graph%20theory%20is%20defined%20as%20e,node%20to%20the%20entry%20node.

## Interesting use case that I worked for 

- white box testing

## How to use it with tools ? 
- Visual studio
- The primary tool I use for identifying CC is Visual Studio 2022/2019. It's under _Analyze -> Calculate Code Metrics._

Some other tools you could use to identify CC include JetBrain's Resharper ([review](https://www.danylkoweb.com/Blog/review-resharper-9-ultimate-8U "Go to Review: Resharper 9 Ultimate")) and NDepend ([review](https://www.danylkoweb.com/Blog/review-ndepend-20201-QK "Go to Review: NDepend 2020.1")) for static code analysis.

As you can guess, there are a number of tools to assist with cyclomatic complexity while writing code.

- [**Visual Studio**](https://visualstudio.microsoft.com/downloads/ "Go to Visual Studio on visualstudio.microsoft.com") - There are plenty of extensions out there along with built-in functionality for cyclomatic complexity out-of-the-box.
- [**Visual Studio Code**](https://code.visualstudio.com/ "Go to Visual Studio Code on code.visualstudio.com") - Various extensions are available like [CodeMetrics](https://marketplace.visualstudio.com/items?itemName=EF8980CD-E5F2-4113-92AE-5789026D677C.MetricsAnalyzer "Go to CodeMetrics on marketplace.visualstudio.com").
- [**JetBrains ReSharper**](https://www.jetbrains.com/resharper/ "Go to Resharper on jetbrains.com") ([Review](https://www.danylkoweb.com/Blog/review-resharper-9-ultimate-8U "Go to Resharper 9 Ultimate Review"))
- [**CodeRush**](https://www.devexpress.com/products/coderush/ "Go to CodeRush on devexpress.com")



## Usage

vcxvxcvxc







## Breaking the system

## Special attention here 
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

## Cons
- Vague idea in original paper
	- different tools have diff implement
- doesnt have the entire picture ? 
	- external functions, API 
- cheating ? 
	- https://softwareengineering.stackexchange.com/questions/344358/cyclomatic-complexity-spread-over-non-reusable-functions
	- advocating function based cyclomatic ?
	- hide spagetti code

## Alternatives
- Cognitive complexity
	- https://www.sonarsource.com/blog/cognitive-complexity-because-testability-understandability/
- MISRA
- https://brainhub.eu/library/measuring-cyclomatic-complexity



## References 
- mccabe paper



# personal opinions
I consider it more of a sniff test than anything to rigidly adhere to.

Usually if my function's cyclomatic complexity is too high, I know it with or without a sniffer telling me so.


A metric that becomes a target ceases to be a good metric." - Goodhart's Law
Donald knuth