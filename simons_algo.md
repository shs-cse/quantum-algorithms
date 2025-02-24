# A Note On Simon's Algorithm

Simon's algorithm, introduced by Daniel Simon in 1994, was the first quantum algorithm to truly demonstrate an exponential speedup over classical computation. While earlier quantum algorithms, such as Deutsch-Jozsa and Bernstein-Vazirani, showcased quantum parallelism, they only achieved a polynomial advantage over randomized classical algorithms. Simon’s result was groundbreaking because it provided the first provable separation between quantum and classical complexity in a computational problem. This discovery shattered previous assumptions about the limits of classical computation and provided strong evidence that quantum computers could fundamentally outperform their classical counterparts for certain tasks.

### Historical Background
There is an interesting story<sup>[[1]][[2]]</sup> behind Simon’s work. In 1993, Simon initially submitted his paper to STOC (ACM Symposium on the Theory of Computing), one of the two most prestigious conferences in theoretical computer science (alongside FOCS, the IEEE Symposium on Foundations of Computer Science). However, the paper was rejected, as many in the classical computing community remained skeptical of quantum computing's significance.

One of the members of the STOC 1993 program committee was Peter Shor, who immediately recognized the importance of Simon’s result. Although he could not convince the rest of the committee to accept the paper, he was deeply inspired by the idea. Shor realized that he could extend Simon’s approach to solve the discrete logarithm problem, which underpins many cryptographic protocols. This insight led him to develop his quantum algorithm for integer factorization, now known as Shor’s algorithm.

After developing his algorithm, Shor personally requested Simon to submit both of their papers together to FOCS. Simon resubmitted his work, and this time, it was accepted alongside Shor’s paper. Both papers were published together in FOCS 1994, marking a pivotal moment in quantum computing. Simon’s result provided the first exponential separation between quantum and classical complexity, while Shor’s algorithm demonstrated the real-world impact of quantum computation by breaking widely used cryptographic schemes. Their publication in one of the most prestigious theoretical computer science conferences firmly established quantum algorithms as a transformative field of research.
## Problem Description
The simpler (non-decision) version of the Simon's problem considers a two-to-one function <img src="https://i.upmath.me/svg/f%3A%5Cmathbb%7BB%7D%5En%5Cto%5Cmathbb%7BB%7D%5Em" alt="f:\mathbb{B}^n\to\mathbb{B}^m" /> that takes in an <img src="https://i.upmath.me/svg/n" alt="n" />-bit binary string and spits out an <img src="https://i.upmath.me/svg/m" alt="m" />-bit binary string, where <img src="https://i.upmath.me/svg/m%5Cgeq%20n-1" alt="m\geq n-1" />. The function is promised to be a balanced two-to-one function such that for <img src="https://i.upmath.me/svg/f(%5Cpmb%7Bx_1%7D)%3Df(%5Cpmb%7Bx_2%7D)" alt="f(\pmb{x_1})=f(\pmb{x_2})" /> if and only if <img src="https://i.upmath.me/svg/%5Cpmb%7Bx_2%7D%3D%5Cpmb%7Bx_1%7D%5Coplus%5Cpmb%7Bs%7D" alt="\pmb{x_2}=\pmb{x_1}\oplus\pmb{s}" /> for some hidden binary string <img src="https://i.upmath.me/svg/%5Cpmb%7Bs%7D%5Cin%5Cmathbb%7BB%7D%5En%7D" alt="\pmb{s}\in\mathbb{B}^n}" />. 

In other words, <img src="https://i.upmath.me/svg/f(%5Cpmb%7Bx%7D)%3Df(%5Cpmb%7Bx%7D%5Coplus%5Cpmb%7Bs%7D)" alt="f(\pmb{x})=f(\pmb{x}\oplus\pmb{s})" /> and this output is unique only for that specific <img src="https://i.upmath.me/svg/%5Cpmb%7Bx%7D" alt="\pmb{x}" /> and <img src="https://i.upmath.me/svg/%5Cpmb%7Bx%7D%5Coplus%5Cpmb%7Bs%7D" alt="\pmb{x}\oplus\pmb{s}" />. However, we do not know the value of <img src="https://i.upmath.me/svg/%5Cpmb%7Bs%7D" alt="\pmb{s}" /> neither do we have access to the inner workings of the function <img src="https://i.upmath.me/svg/f(%5Cpmb%7Bx%7D)" alt="f(\pmb{x})" /> (i.e. oracle/black box model). The goal is to develop an algorithm to determine the binary string <img src="https://i.upmath.me/svg/%5Cpmb%7Bs%7D" alt="\pmb{s}" />.

The original (decision) version of the Simon's problem also considers the case where <img src="https://i.upmath.me/svg/f(%5Cpmb%7Bx%7D)" alt="f(\pmb{x})" /> is a one-to-one function. This natural extension does not change the promise and only happens when <img src="https://i.upmath.me/svg/%5Cpmb%7Bs%7D%3D%5Cpmb%7B0%7D" alt="\pmb{s}=\pmb{0}" />. Since the function can now either be a one-to-one or a two-to-one function, <img src="https://i.upmath.me/svg/m" alt="m" /> is at least <img src="https://i.upmath.me/svg/n" alt="n" />.

### Problem Statement
> * **Oracle:** A function <img src="https://i.upmath.me/svg/f%3A%5Cmathbb%7BB%7D%5En%5Cto%5Cmathbb%7BB%7D%5Em" alt="f:\mathbb{B}^n\to\mathbb{B}^m" /> where <img src="https://i.upmath.me/svg/m%5Cgeq%20n" alt="m\geq n" />.
> * **Promise:** <img src="https://i.upmath.me/svg/f(%5Cpmb%7Bx_1%7D)%3Df(%5Cpmb%7Bx_2%7D)%5Ciff%5Cpmb%7Bx_2%7D%3D%5Cpmb%7Bx_1%7D%5Coplus%5Cpmb%7Bs%7D" alt="f(\pmb{x_1})=f(\pmb{x_2})\iff\pmb{x_2}=\pmb{x_1}\oplus\pmb{s}" />
> * **Problem:** Find <img src="https://i.upmath.me/svg/%5Cpmb%7Bs%7D" alt="\pmb{s}" />.
### Example
Let's look at a specific example where <img src="https://i.upmath.me/svg/m%3Dn%3D3%2C%20%5Cpmb%7Bs%7D%3D011_2%3D%5Cpmb3" alt="m=n=3, \pmb{s}=011_2=\pmb3" /> and the two-to-one function <img src="https://i.upmath.me/svg/f(%5Cpmb%7Bx%7D)%3D%5Cmax(%5Cpmb%7Bx%7D%2C%5Cpmb%7Bx%7D%5Coplus%5Cpmb%7Bs%7D)" alt="f(\pmb{x})=\max(\pmb{x},\pmb{x}\oplus\pmb{s})" />. Please note that here max is just typical maximum of two numbers taking <img src="https://i.upmath.me/svg/%5Cpmb%7Bx%7D" alt="\pmb{x}" /> as a binary number. Often times for simplicity, we will represent a binary string by just the decimal representation (e.g. <img src="https://i.upmath.me/svg/011_2" alt="011_2" /> as <img src="https://i.upmath.me/svg/%5Cpmb3" alt="\pmb3" />). Below is a table evaluated for all possible <img src="https://i.upmath.me/svg/%5Cpmb%7Bx%7D" alt="\pmb{x}" /> (both in binary and decimal form):

<img src="https://i.upmath.me/svg/%0A%5Cbegin%7Btabular%7D%7Bccc%7Cc%20c%20c%7Cc%7D%0A%24%5Cpmb%7Bs%7D%24%20%26%20%24%5Cpmb%7Bx%7D%24%20%26%20%24%5Cpmb%7Bx%7D%5Coplus%5Cpmb%7Bs%7D%24%20%26%20%24%5Cmax(%5Cpmb%7Bx%7D%2C%5Cpmb%7Bx%7D%5Coplus%5Cpmb%7Bs%7D)%24%20%26%20%5Cphantom%7Bhuge%20gap%7D%20%26%20%24%5Cpmb%7Bx%7D%24%20%26%20%24f(%5Cpmb%7Bx%7D)%24%5C%5C%0A%5Ccline%7B1-4%7D%5Ccline%7B6-7%7D%0A%20%20%20%20%26%20000%20%26%20011%20%26%20011%20%26%26%20%5Cpmb0%20%26%20%5Cpmb3%5C%5C%0A%20%20%20%20%26%20001%20%26%20010%20%26%20010%20%26%26%20%5Cpmb1%20%26%20%5Cpmb2%5C%5C%0A%20%20%20%20%26%20010%20%26%20001%20%26%20010%20%26%26%20%5Cpmb2%20%26%20%5Cpmb2%5C%5C%0A011%20%26%20011%20%26%20000%20%26%20011%20%26%26%20%5Cpmb3%20%26%20%5Cpmb3%5C%5C%0A%20%20%20%20%26%20100%20%26%20111%20%26%20111%20%26%26%20%5Cpmb4%20%26%20%5Cpmb7%5C%5C%0A%20%20%20%20%26%20101%20%26%20110%20%26%20110%20%26%26%20%5Cpmb5%20%26%20%5Cpmb6%5C%5C%0A%20%20%20%20%26%20110%20%26%20101%20%26%20110%20%26%26%20%5Cpmb6%20%26%20%5Cpmb6%5C%5C%0A%20%20%20%20%26%20111%20%26%20100%20%26%20111%20%26%26%20%5Cpmb7%20%26%20%5Cpmb7%5C%5C%0A%5Cend%7Btabular%7D%0A" alt="
\begin{tabular}{ccc|c c c|c}
$\pmb{s}$ &amp; $\pmb{x}$ &amp; $\pmb{x}\oplus\pmb{s}$ &amp; $\max(\pmb{x},\pmb{x}\oplus\pmb{s})$ &amp; \phantom{huge gap} &amp; $\pmb{x}$ &amp; $f(\pmb{x})$\\
\cline{1-4}\cline{6-7}
    &amp; 000 &amp; 011 &amp; 011 &amp;&amp; \pmb0 &amp; \pmb3\\
    &amp; 001 &amp; 010 &amp; 010 &amp;&amp; \pmb1 &amp; \pmb2\\
    &amp; 010 &amp; 001 &amp; 010 &amp;&amp; \pmb2 &amp; \pmb2\\
011 &amp; 011 &amp; 000 &amp; 011 &amp;&amp; \pmb3 &amp; \pmb3\\
    &amp; 100 &amp; 111 &amp; 111 &amp;&amp; \pmb4 &amp; \pmb7\\
    &amp; 101 &amp; 110 &amp; 110 &amp;&amp; \pmb5 &amp; \pmb6\\
    &amp; 110 &amp; 101 &amp; 110 &amp;&amp; \pmb6 &amp; \pmb6\\
    &amp; 111 &amp; 100 &amp; 111 &amp;&amp; \pmb7 &amp; \pmb7\\
\end{tabular}
" />

### Visualization
We can represent each input <img src="https://i.upmath.me/svg/%5Cpmb%7Bx%7D" alt="\pmb{x}" /> with a node and connecting them with an edge if their outputs match. For better visualization, we can paint them with the same color to denote they are related. If the oracle is a two-to-one function, every node will be connected exactly one other node<sup>[[3]]</sup>. However, if the oracle is a one-to-one function, all the nodes will be disjoint.

For our specific example, we can represent all the input using the vertices of a (Hamming) cube. Since <img src="https://i.upmath.me/svg/f(%5Cpmb0)%3Df(%5Cpmb3)" alt="f(\pmb0)=f(\pmb3)" />, both of them are of same color and are connected by a line. From the diagram, it is easy to see there is a certain symmetry in Simon's problem:

<img src="https://i.upmath.me/svg/%5Cbegin%7Btikzpicture%7D%5Bscale%3D2%5D%0A%25%0A%20%20%20%20%25%20Define%20colors%0A%20%20%20%20%5Cdefinecolor%7Bc1%7D%7BHTML%7D%7Bea5545%7D%0A%20%20%20%20%5Cdefinecolor%7Bc2%7D%7BHTML%7D%7Bf46a9b%7D%0A%20%20%20%20%5Cdefinecolor%7Bc3%7D%7BHTML%7D%7Bef9b20%7D%0A%20%20%20%20%5Cdefinecolor%7Bc4%7D%7BHTML%7D%7Bedbf33%7D%0A%20%20%20%20%5Cdefinecolor%7Bc5%7D%7BHTML%7D%7Bede15b%7D%0A%20%20%20%20%5Cdefinecolor%7Bc6%7D%7BHTML%7D%7Bbdcf32%7D%0A%20%20%20%20%5Cdefinecolor%7Bc7%7D%7BHTML%7D%7B87bc45%7D%0A%20%20%20%20%5Cdefinecolor%7Bc8%7D%7BHTML%7D%7B27aeef%7D%0A%20%20%20%20%5Cdefinecolor%7Bc9%7D%7BHTML%7D%7Bb33dc6%7D%0A%25%0A%20%20%20%20%25%20Define%20vertex%20positions%0A%20%20%20%20%5Ccoordinate%20(V0)%20at%20(0%2C0%2C0)%3B%0A%20%20%20%20%5Ccoordinate%20(V1)%20at%20(0%2C1%2C0)%3B%0A%20%20%20%20%5Ccoordinate%20(V2)%20at%20(0%2C0%2C-1)%3B%0A%20%20%20%20%5Ccoordinate%20(V3)%20at%20(0%2C1%2C-1)%3B%0A%20%20%20%20%5Ccoordinate%20(V4)%20at%20(1%2C0%2C0)%3B%0A%20%20%20%20%5Ccoordinate%20(V5)%20at%20(1%2C1%2C0)%3B%0A%20%20%20%20%5Ccoordinate%20(V6)%20at%20(1%2C0%2C-1)%3B%0A%20%20%20%20%5Ccoordinate%20(V7)%20at%20(1%2C1%2C-1)%3B%0A%25%0A%20%20%20%20%25%20Draw%20cube%20edges%20(dashed)%0A%20%20%20%20%5Cforeach%20%5Ci%2F%5Cj%20in%20%7B0%2F1%2C%200%2F2%2C%201%2F3%2C%202%2F3%2C%204%2F5%2C%204%2F6%2C%205%2F7%2C%206%2F7%2C%200%2F4%2C%201%2F5%2C%202%2F6%2C%203%2F7%7D%20%7B%0A%20%20%20%20%20%20%20%20%5Cdraw%5Bdotted%2C%20black%5D%20(V%5Ci)%20--%20(V%5Cj)%3B%0A%20%20%20%20%7D%0A%25%0A%20%20%20%20%25%20Draw%20special%20edges%0A%20%20%20%20%5Cdraw%5Bc1%2C%20ultra%20thick%5D%20(V0)%20--%20(V3)%3B%0A%20%20%20%20%5Cdraw%5Bc3%2C%20ultra%20thick%5D%20(V1)%20--%20(V2)%3B%0A%20%20%20%20%5Cdraw%5Bc5%2C%20ultra%20thick%5D%20(V4)%20--%20(V7)%3B%0A%20%20%20%20%5Cdraw%5Bc7%2C%20ultra%20thick%5D%20(V5)%20--%20(V6)%3B%0A%25%0A%20%20%20%20%25%20Draw%20vertices%20and%20labels%0A%20%20%20%20%5Cforeach%20%5Ci%2F%5Ccol%20in%20%7B0%2Fc1%2C%201%2Fc3%2C%202%2Fc3%2C%203%2Fc1%2C%204%2Fc5%2C%205%2Fc7%2C%206%2Fc7%2C%207%2Fc5%7D%20%7B%0A%20%20%20%20%20%20%20%20%5Cnode%5Bcircle%2C%20fill%3D%5Ccol%2C%20draw%5D%20at%20(V%5Ci)%20%7B%7D%3B%0A%20%20%20%20%20%20%20%20%5Cnode%5Babove%20right%5D%20at%20(V%5Ci)%20%7B%24%5Cpmb%7B%5Ci%7D%24%7D%3B%0A%20%20%20%20%7D%0A%25%0A%5Cend%7Btikzpicture%7D" alt="\begin{tikzpicture}[scale=2]
%
    % Define colors
    \definecolor{c1}{HTML}{ea5545}
    \definecolor{c2}{HTML}{f46a9b}
    \definecolor{c3}{HTML}{ef9b20}
    \definecolor{c4}{HTML}{edbf33}
    \definecolor{c5}{HTML}{ede15b}
    \definecolor{c6}{HTML}{bdcf32}
    \definecolor{c7}{HTML}{87bc45}
    \definecolor{c8}{HTML}{27aeef}
    \definecolor{c9}{HTML}{b33dc6}
%
    % Define vertex positions
    \coordinate (V0) at (0,0,0);
    \coordinate (V1) at (0,1,0);
    \coordinate (V2) at (0,0,-1);
    \coordinate (V3) at (0,1,-1);
    \coordinate (V4) at (1,0,0);
    \coordinate (V5) at (1,1,0);
    \coordinate (V6) at (1,0,-1);
    \coordinate (V7) at (1,1,-1);
%
    % Draw cube edges (dashed)
    \foreach \i/\j in {0/1, 0/2, 1/3, 2/3, 4/5, 4/6, 5/7, 6/7, 0/4, 1/5, 2/6, 3/7} {
        \draw[dotted, black] (V\i) -- (V\j);
    }
%
    % Draw special edges
    \draw[c1, ultra thick] (V0) -- (V3);
    \draw[c3, ultra thick] (V1) -- (V2);
    \draw[c5, ultra thick] (V4) -- (V7);
    \draw[c7, ultra thick] (V5) -- (V6);
%
    % Draw vertices and labels
    \foreach \i/\col in {0/c1, 1/c3, 2/c3, 3/c1, 4/c5, 5/c7, 6/c7, 7/c5} {
        \node[circle, fill=\col, draw] at (V\i) {};
        \node[above right] at (V\i) {$\pmb{\i}$};
    }
%
\end{tikzpicture}" />

### General visualization

If <img src="https://i.upmath.me/svg/f(%5Cpmb%7Bx%7D)" alt="f(\pmb{x})" /> is two-to-one:

<img src="https://i.upmath.me/svg/%0A%5Cbegin%7Btikzpicture%7D%0A%20%20%20%20%25%20Define%20colors%0A%20%20%20%20%5Cdefinecolor%7Bc1%7D%7BHTML%7D%7Bea5545%7D%0A%20%20%20%20%5Cdefinecolor%7Bc2%7D%7BHTML%7D%7Bf46a9b%7D%0A%20%20%20%20%5Cdefinecolor%7Bc3%7D%7BHTML%7D%7Bef9b20%7D%0A%20%20%20%20%5Cdefinecolor%7Bc4%7D%7BHTML%7D%7Bedbf33%7D%0A%20%20%20%20%5Cdefinecolor%7Bc5%7D%7BHTML%7D%7Bede15b%7D%0A%20%20%20%20%5Cdefinecolor%7Bc6%7D%7BHTML%7D%7Bbdcf32%7D%0A%20%20%20%20%5Cdefinecolor%7Bc7%7D%7BHTML%7D%7B87bc45%7D%0A%20%20%20%20%5Cdefinecolor%7Bc8%7D%7BHTML%7D%7B27aeef%7D%0A%20%20%20%20%5Cdefinecolor%7Bc9%7D%7BHTML%7D%7Bb33dc6%7D%0A%20%20%20%20%25%0A%20%20%20%20%25%20Spacing%20variable%0A%20%20%20%20%5Cdef%5Cdx%7B2.5%7D%0A%20%20%20%20%25%0A%20%20%20%20%25%20Draw%20node%20pairs%0A%20%20%20%20%5Cforeach%20%5Ci%2F%5Cclr%20in%20%7B1%2Fc1%2C%202%2Fc3%2C%203%2Fc5%2C%204%2Fc7%7D%20%7B%0A%20%20%20%20%20%20%20%20%5Cnode%5Bcircle%2C%20fill%3D%5Cclr%2C%20draw%5D%20(a%5Ci)%20at%20(%5Ci*%5Cdx%2C%201)%20%7B%7D%3B%0A%20%20%20%20%20%20%20%20%5Cnode%20at%20(%5Ci*%5Cdx%2C1.5)%20%7B%24%5Cpmb%7Bx_%5Ci%7D%24%7D%3B%0A%20%20%20%20%20%20%20%20%5Cnode%5Bcircle%2C%20fill%3D%5Cclr%2C%20draw%5D%20(b%5Ci)%20at%20(%5Ci*%5Cdx%2C%20-1)%20%7B%7D%3B%0A%20%20%20%20%20%20%20%20%5Cnode%20at%20(%5Ci*%5Cdx%2C-1.5)%20%7B%24%5Cpmb%7Bx_%5Ci%7D%5Coplus%5Cpmb%7Bs%7D%24%7D%3B%0A%20%20%20%20%20%20%20%20%5Cdraw%5B%5Cclr%2C%20ultra%20thick%5D%20(a%5Ci)%20--%20(b%5Ci)%3B%0A%20%20%20%20%7D%0A%20%20%20%20%25%0A%20%20%20%20%25%20Draw%20dots%20for%20continuation%0A%20%20%20%20%5Cnode%5Bscale%3D2%5D%20at%20(5*%5Cdx%2C%200)%20%7B%5Cdots%7D%3B%0A%5Cend%7Btikzpicture%7D%0A" alt="
\begin{tikzpicture}
    % Define colors
    \definecolor{c1}{HTML}{ea5545}
    \definecolor{c2}{HTML}{f46a9b}
    \definecolor{c3}{HTML}{ef9b20}
    \definecolor{c4}{HTML}{edbf33}
    \definecolor{c5}{HTML}{ede15b}
    \definecolor{c6}{HTML}{bdcf32}
    \definecolor{c7}{HTML}{87bc45}
    \definecolor{c8}{HTML}{27aeef}
    \definecolor{c9}{HTML}{b33dc6}
    %
    % Spacing variable
    \def\dx{2.5}
    %
    % Draw node pairs
    \foreach \i/\clr in {1/c1, 2/c3, 3/c5, 4/c7} {
        \node[circle, fill=\clr, draw] (a\i) at (\i*\dx, 1) {};
        \node at (\i*\dx,1.5) {$\pmb{x_\i}$};
        \node[circle, fill=\clr, draw] (b\i) at (\i*\dx, -1) {};
        \node at (\i*\dx,-1.5) {$\pmb{x_\i}\oplus\pmb{s}$};
        \draw[\clr, ultra thick] (a\i) -- (b\i);
    }
    %
    % Draw dots for continuation
    \node[scale=2] at (5*\dx, 0) {\dots};
\end{tikzpicture}
" />

If <img src="https://i.upmath.me/svg/f(%5Cpmb%7Bx%7D)" alt="f(\pmb{x})" /> is one-to-one:

<img src="https://i.upmath.me/svg/%0A%5Cbegin%7Btikzpicture%7D%0A%20%20%20%20%25%20Define%20colors%0A%20%20%20%20%5Cdefinecolor%7Bc1%7D%7BHTML%7D%7Bea5545%7D%0A%20%20%20%20%5Cdefinecolor%7Bc2%7D%7BHTML%7D%7Bf46a9b%7D%0A%20%20%20%20%5Cdefinecolor%7Bc3%7D%7BHTML%7D%7Bef9b20%7D%0A%20%20%20%20%5Cdefinecolor%7Bc4%7D%7BHTML%7D%7Bedbf33%7D%0A%20%20%20%20%5Cdefinecolor%7Bc5%7D%7BHTML%7D%7Bede15b%7D%0A%20%20%20%20%5Cdefinecolor%7Bc6%7D%7BHTML%7D%7Bbdcf32%7D%0A%20%20%20%20%5Cdefinecolor%7Bc7%7D%7BHTML%7D%7B87bc45%7D%0A%20%20%20%20%5Cdefinecolor%7Bc8%7D%7BHTML%7D%7B27aeef%7D%0A%20%20%20%20%5Cdefinecolor%7Bc9%7D%7BHTML%7D%7Bb33dc6%7D%0A%20%20%20%20%25%0A%20%20%20%20%25%20Spacing%20variable%0A%20%20%20%20%5Cdef%5Cdx%7B2.5%7D%0A%20%20%20%20%25%0A%20%20%20%20%25%20Draw%20node%20pairs%0A%20%20%20%20%5Cforeach%20%5Ci%2F%5Cclr%20in%20%7B1%2Fc1%2C%202%2Fc2%2C%203%2Fc3%2C%204%2Fc4%7D%20%7B%0A%20%20%20%20%20%20%20%20%5Cnode%5Bcircle%2C%20fill%3D%5Cclr%2C%20draw%5D%20(a%5Ci)%20at%20(%5Ci*%5Cdx%2C%200)%20%7B%7D%3B%0A%20%20%20%20%20%20%20%20%5Cnode%20at%20(%5Ci*%5Cdx%2C0.5)%20%7B%24%5Cpmb%7Bx_%5Ci%7D%24%7D%3B%0A%20%20%20%20%7D%0A%20%20%20%20%25%0A%20%20%20%20%25%20Draw%20dots%20for%20continuation%0A%20%20%20%20%5Cnode%5Bscale%3D2%5D%20at%20(5*%5Cdx%2C%200)%20%7B%5Cdots%7D%3B%0A%5Cend%7Btikzpicture%7D%0A" alt="
\begin{tikzpicture}
    % Define colors
    \definecolor{c1}{HTML}{ea5545}
    \definecolor{c2}{HTML}{f46a9b}
    \definecolor{c3}{HTML}{ef9b20}
    \definecolor{c4}{HTML}{edbf33}
    \definecolor{c5}{HTML}{ede15b}
    \definecolor{c6}{HTML}{bdcf32}
    \definecolor{c7}{HTML}{87bc45}
    \definecolor{c8}{HTML}{27aeef}
    \definecolor{c9}{HTML}{b33dc6}
    %
    % Spacing variable
    \def\dx{2.5}
    %
    % Draw node pairs
    \foreach \i/\clr in {1/c1, 2/c2, 3/c3, 4/c4} {
        \node[circle, fill=\clr, draw] (a\i) at (\i*\dx, 0) {};
        \node at (\i*\dx,0.5) {$\pmb{x_\i}$};
    }
    %
    % Draw dots for continuation
    \node[scale=2] at (5*\dx, 0) {\dots};
\end{tikzpicture}
" />


## Classical Approach
One naive approach might be to evaluate <img src="https://i.upmath.me/svg/f(%5Cpmb0)" alt="f(\pmb0)" /> and keep querying until we find an <img src="https://i.upmath.me/svg/%5Cpmb%7Bx%7D" alt="\pmb{x}" /> for which we get <img src="https://i.upmath.me/svg/f(%5Cpmb%7Bx%7D)%3Df(%5Cpmb0)" alt="f(\pmb{x})=f(\pmb0)" />. Then, from the promise, <img src="https://i.upmath.me/svg/%5Cpmb%7Bx%7D%3D%5Cpmb0%5Coplus%5Cpmb%7Bs%7D%3D%5Cpmb%7Bs%7D" alt="\pmb{x}=\pmb0\oplus\pmb{s}=\pmb{s}" />. However, in the worst case scenario, we will have to query all possible inputs until we get a match. So a total of <img src="https://i.upmath.me/svg/2%5E%7Bn%7D-1" alt="2^{n}-1" /> times. On average, this will get improved to <img src="https://i.upmath.me/svg/2%5E%7Bn-1%7D" alt="2^{n-1}" /> queries.

There is a better approach (still exponential though), which checks against all previously queried outputs. This approach is completely analogous to the classic birthday paradox. Here is the (python) code for our procedure:

```py
q = dict()
for x in range(2**n):
  y = f(x)
  if y not in q:
    q[y] = x
  else:
    print("s =", x ^ q[y])
    break
```
### Complexity Analysis
We can find the query complexity (number of times `f(x)` is executed), by computing the probability of the `if` and `else` block individually. However, since the algorithm stops when it finds a previously queried output (collision), it is a probabilistic algorithm. So we need to find the expectation value of the query complexity.

Say, we have queried the function <img src="https://i.upmath.me/svg/(k-1)" alt="(k-1)" /> times without any collision. If <img src="https://i.upmath.me/svg/f(%5Cpmb%7Bx%7D)" alt="f(\pmb{x})" /> is a two-to-one function, even though <img src="https://i.upmath.me/svg/N%3D2%5En" alt="N=2^n" /> different inputs are possible, there are only <img src="https://i.upmath.me/svg/M%3D%5Ctfrac%7BN%7D%7B2%7D%3D2%5E%7Bn-1%7D" alt="M=\tfrac{N}{2}=2^{n-1}" /> unique outputs. In the <img src="https://i.upmath.me/svg/k" alt="k" />-th iteration, the probability of getting a collision is: 

<img src="https://i.upmath.me/svg/%5CPr(%5Ctext%7Bcollision%20on%20the%20%24k%24-th%20iteration%7D)%3D%5Cfrac%7Bk-1%7D%7B2%5E%7Bn-1%7D%7D%3D%5Cfrac%7Bk-1%7D%7BN%2F2%7D" alt="\Pr(\text{collision on the $k$-th iteration})=\frac{k-1}{2^{n-1}}=\frac{k-1}{N/2}" />

So, the probability of getting no collision with any output from the first iteration to <img src="https://i.upmath.me/svg/(k-1)" alt="(k-1)" />-th iteration is:

<img src="https://i.upmath.me/svg/%5CPr(%5Ctext%7Bno%20collision%20after%20%24k%24%20iterations%7D)%3D%5Cprod_%7Bj%3D0%7D%5E%7Bk-1%7D%5Cleft(1-%5Cfrac%7B2j%7D%7BN%7D%5Cright)" alt="\Pr(\text{no collision after $k$ iterations})=\prod_{j=0}^{k-1}\left(1-\frac{2j}{N}\right)" />

Taking logarithm on both sides and approximating <img src="https://i.upmath.me/svg/%5Cln(1%2Bx)%5Clessapprox%20x" alt="\ln(1+x)\lessapprox x" /> when <img src="https://i.upmath.me/svg/x%5Cto0" alt="x\to0" />, we get,

<img src="https://i.upmath.me/svg/%5Cln(%5CPr(%5Ctext%7Bno%20collision%7D))%20%3D%20%5Csum_%7Bj%3D0%7D%5E%7Bk-1%7D%5Cln%5Cleft(1-%5Cfrac%7B2j%7D%7BN%7D%5Cright)%20%5Clessapprox%20%5Csum_%7Bj%3D0%7D%5E%7Bk-1%7D-%5Cfrac%7B2j%7D%7BN%7D" alt="\ln(\Pr(\text{no collision})) = \sum_{j=0}^{k-1}\ln\left(1-\frac{2j}{N}\right) \lessapprox \sum_{j=0}^{k-1}-\frac{2j}{N}" />

Since <img src="https://i.upmath.me/svg/%5Csum_%7Bj%3D0%7D%5E%7Bk-1%7Dj%3D%5Cfrac%7Bk(k-1)%7D%7B2%7D%5Cgtrapprox%5Cfrac%7Bk%5E2%7D%7B2%7D" alt="\sum_{j=0}^{k-1}j=\frac{k(k-1)}{2}\gtrapprox\frac{k^2}{2}" />, we have, 

<img src="https://i.upmath.me/svg/%5Cln(%5CPr(%5Ctext%7Bno%20collision%7D))%20%5Clessapprox%20-%5Cfrac%7B2%7D%7BN%7D%5Csum_%7Bj%3D0%7D%5E%7Bk-1%7Dj%20%5Clessapprox%20-%5Cfrac%7Bk%5E2%7D%7BN%7D" alt="\ln(\Pr(\text{no collision})) \lessapprox -\frac{2}{N}\sum_{j=0}^{k-1}j \lessapprox -\frac{k^2}{N}" />

<img src="https://i.upmath.me/svg/%5Ctherefore%20%5CPr(%5Ctext%7Bno%20collision%7D)%20%5Clessapprox%20%5Cexp%5Cleft(-%5Cfrac%7Bk%5E2%7D%7BN%7D%5Cright)" alt="\therefore \Pr(\text{no collision}) \lessapprox \exp\left(-\frac{k^2}{N}\right)" />

Since our algorithm will stop once it finds a collision, it is reassuring that we have an exponential decay function here. It means that, the more we query (i.e. <img src="https://i.upmath.me/svg/k" alt="k" /> increases), chance of not colliding decreases exponentially (i.e. <img src="https://i.upmath.me/svg/%5CPr(%5Ctext%7Bcollision%7D)" alt="\Pr(\text{collision})" /> increases). To achieve a probability of (say) <img src="https://i.upmath.me/svg/90%5C%25" alt="90\%" /> of collision, we need to query <img src="https://i.upmath.me/svg/k" alt="k" /> times where,

<img src="https://i.upmath.me/svg/%5CPr(%5Ctext%7Bno%20collision%7D)%3D1-0.9%3D0.1%5Clessapprox%20%5Cexp%5Cleft(-%5Cfrac%7Bk%5E2%7D%7BN%7D%5Cright)" alt="\Pr(\text{no collision})=1-0.9=0.1\lessapprox \exp\left(-\frac{k^2}{N}\right)" />

<img src="https://i.upmath.me/svg/%5Cimplies%20k%5Cgtrapprox%5Csqrt%7B-N%5Cln0.1%7D%3D%5Csqrt%7BN%5Cln10%7D" alt="\implies k\gtrapprox\sqrt{-N\ln0.1}=\sqrt{N\ln10}" />

<img src="https://i.upmath.me/svg/%5Ctherefore%20k%20%3D%20%5COmega%5Cleft(%5Csqrt%7BN%7D%5Cright)%20%3D%20%5COmega%5Cleft(%5Csqrt%7B2%5En%7D%5Cright)" alt="\therefore k = \Omega\left(\sqrt{N}\right) = \Omega\left(\sqrt{2^n}\right)" />

In conclusion, to ensure a high probabiliy of collision i.e. successfully stopping the program, we need <img src="https://i.upmath.me/svg/%5COmega%5Cleft(%5Csqrt%7B2%5En%7D%5Cright)" alt="\Omega\left(\sqrt{2^n}\right)" /> queries.

## Simon's Algorithm
Simon's algorithm consists of two parts: quantum subroutine and classical post-processing. The following quantum circuit is executed <img src="https://i.upmath.me/svg/%5Cmathcal%7BO%7D(n)" alt="\mathcal{O}(n)" /> times and then the measured data is used for the post-processing part. There we have to solve a system of linear equations to finally get <img src="https://i.upmath.me/svg/%5Cpmb%7Bs%7D" alt="\pmb{s}" />.

![Quantum Circuit for Simon's Algorithm](https://www.researchgate.net/publication/350180612/figure/fig12/AS:1003308181381161@1616218719487/The-quantum-circuit-of-Simons-algorithm-The-measurement-in-the-dotted-box-could-be.png)
### Quantum Subroutine
The first <img src="https://i.upmath.me/svg/n" alt="n" />-qubit register is the input register and the last <img src="https://i.upmath.me/svg/m" alt="m" />-qubit register is the output register. After passing through the first Hadamard gate, we will have an equal superposition of all <img src="https://i.upmath.me/svg/N%3D2%5En" alt="N=2^n" /> input states,

<img src="https://i.upmath.me/svg/%5Cdef%5Cket%231%7B%5Cleft%7C%5Censuremath%7B%231%7D%5Cright%5Crangle%7D%0A%5Cdef%5Cketpmb%231%7B%5Cket%7B%5Cpmb%7B%231%7D%7D%7D%0AH%5E%7B%5Cotimes%20n%7D%5Cketpmb%7B0%7D%20%3D%20%5Cfrac%7B1%7D%7B%5Csqrt%7BN%7D%7D%5Csum_%7B%5Cpmb%7Bx%7D%3D%5Cpmb0%7D%5E%7B%5Cpmb%7BN-1%7D%7D%5Cketpmb%7Bx%7D%0A" alt="\def\ket#1{\left|\ensuremath{#1}\right\rangle}
\def\ketpmb#1{\ket{\pmb{#1}}}
H^{\otimes n}\ketpmb{0} = \frac{1}{\sqrt{N}}\sum_{\pmb{x}=\pmb0}^{\pmb{N-1}}\ketpmb{x}
" />

Now, we evaluate the oracle on both input and output registers,

<img src="https://i.upmath.me/svg/%5Cdef%5Cket%231%7B%5Cleft%7C%5Censuremath%7B%231%7D%5Cright%5Crangle%7D%0A%5Cdef%5Cketpmb%231%7B%5Cket%7B%5Cpmb%7B%231%7D%7D%7D%0AU_f%5Cleft(%5Cfrac%7B1%7D%7B%5Csqrt%7BN%7D%7D%5Csum_%7B%5Cpmb%7Bx%7D%7D%5Cketpmb%7Bx%7D%5Cketpmb%7B0%7D%5Cright)%20%3D%20%5Cfrac%7B1%7D%7B%5Csqrt%7BN%7D%7D%5Csum_%7B%5Cpmb%7Bx%7D%7D%5Cketpmb%7Bx%7D%5Cket%7Bf(%5Cpmb%7Bx%7D)%7D%0A" alt="\def\ket#1{\left|\ensuremath{#1}\right\rangle}
\def\ketpmb#1{\ket{\pmb{#1}}}
U_f\left(\frac{1}{\sqrt{N}}\sum_{\pmb{x}}\ketpmb{x}\ketpmb{0}\right) = \frac{1}{\sqrt{N}}\sum_{\pmb{x}}\ketpmb{x}\ket{f(\pmb{x})}
" />

To simplify the next steps of the calculation, we measure and discard all the qubits in the output register. Since this is a partial measurement, even though all the output qubits will collapse, the entangled input qubits may remain in superposition. We had equal superposition of all possible inputs before measurement and every output <img src="https://i.upmath.me/svg/%5Cleft%7Cf(%5Cpmb%7Bx%7D)%5Cright%5Crangle" alt="\left|f(\pmb{x})\right\rangle" /> is entangled with the input states <img src="https://i.upmath.me/svg/%5Cleft%7C%5Cpmb%7Bx%7D%5Cright%5Crangle" alt="\left|\pmb{x}\right\rangle" /> and <img src="https://i.upmath.me/svg/%5Cleft%7C%5Cpmb%7Bx%7D%5Coplus%5Cpmb%7Bs%7D%5Cright%5Crangle" alt="\left|\pmb{x}\oplus\pmb{s}\right\rangle" />. For simplicity of the following calculation steps, we are splitting the two cases of oracle function.

#### Two-to-One Case
If the function is a two-to-one (i.e. <img src="https://i.upmath.me/svg/%5Cpmb%7Bs%7D%5Cneq%5Cpmb0" alt="\pmb{s}\neq\pmb0" />), after the measurement the output register will collapse to one specific output, say <img src="https://i.upmath.me/svg/%5Cleft%7Cf(%5Cpmb%7B%5Ctilde%7Bx%7D%7D)%5Cright%5Crangle" alt="\left|f(\pmb{\tilde{x}})\right\rangle" />, and so the post-measurement result will be,

<img src="https://i.upmath.me/svg/%5Cdef%5Cket%231%7B%5Cleft%7C%5Censuremath%7B%231%7D%5Cright%5Crangle%7D%0A%5Cdef%5Cketpmb%231%7B%5Cket%7B%5Cpmb%7B%5Ctilde%7B%231%7D%7D%7D%7D%0A%5Cfrac%7B1%7D%7B%5Csqrt2%7D%5Cbig(%5Cketpmb%7Bx%7D%2B%5Cket%7B%5Cpmb%7B%5Ctilde%7Bx%7D%7D%5Coplus%5Cpmb%7Bs%7D%7D%5Cbig)%5Cket%7Bf(%5Cpmb%7B%5Ctilde%7Bx%7D%7D)%7D%0A" alt="\def\ket#1{\left|\ensuremath{#1}\right\rangle}
\def\ketpmb#1{\ket{\pmb{\tilde{#1}}}}
\frac{1}{\sqrt2}\big(\ketpmb{x}+\ket{\pmb{\tilde{x}}\oplus\pmb{s}}\big)\ket{f(\pmb{\tilde{x}})}
" />

Next, we discard the output qubits and only concern ourselves with the input qubits. After applying the last Hadamard gate we get the state,

<img src="https://i.upmath.me/svg/%5Cdef%5Cket%231%7B%5Cleft%7C%5Censuremath%7B%231%7D%5Cright%5Crangle%7D%0A%5Cdef%5Cketpmb%231%7B%5Cket%7B%5Cpmb%7B%5Ctilde%7B%231%7D%7D%7D%7D%0AH%5E%7B%5Cotimes%20n%7D%5Cfrac%7B1%7D%7B%5Csqrt2%7D%5Cbig(%5Cketpmb%7Bx%7D%2B%5Cket%7B%5Cpmb%7B%5Ctilde%7Bx%7D%7D%5Coplus%5Cpmb%7Bs%7D%7D%5Cbig)%20%3D%20%5Cfrac%7B1%7D%7B%5Csqrt2%7DH%5E%7B%5Cotimes%20n%7D%5Cketpmb%7Bx%7D%20%2B%20%5Cfrac%7B1%7D%7B%5Csqrt2%7DH%5E%7B%5Cotimes%20n%7D%5Cket%7B%5Cpmb%7B%5Ctilde%7Bx%7D%7D%5Coplus%5Cpmb%7Bs%7D%7D%0A" alt="\def\ket#1{\left|\ensuremath{#1}\right\rangle}
\def\ketpmb#1{\ket{\pmb{\tilde{#1}}}}
H^{\otimes n}\frac{1}{\sqrt2}\big(\ketpmb{x}+\ket{\pmb{\tilde{x}}\oplus\pmb{s}}\big) = \frac{1}{\sqrt2}H^{\otimes n}\ketpmb{x} + \frac{1}{\sqrt2}H^{\otimes n}\ket{\pmb{\tilde{x}}\oplus\pmb{s}}
" />

To compute this, we note the fact that,

<img src="https://i.upmath.me/svg/%5Cdef%5Cket%231%7B%5Cleft%7C%5Censuremath%7B%231%7D%5Cright%5Crangle%7D%0A%5Cdef%5Cketpmb%231%7B%5Cket%7B%5Cpmb%7B%231%7D%7D%7D%0AH%5E%7B%5Cotimes%20n%7D%5Cketpmb%7Bk%7D%20%3D%20%5Cfrac%7B1%7D%7B%5Csqrt%7BN%7D%7D%5Csum_%7B%5Cpmb%7Bj%7D%3D%5Cpmb0%7D%5E%7B%5Cpmb%7BN-1%7D%7D%20(-1)%5E%7B%5Cpmb%7Bj%7D%5Codot%5Cpmb%7Bk%7D%7D%5Cketpmb%7Bj%7D%0A" alt="\def\ket#1{\left|\ensuremath{#1}\right\rangle}
\def\ketpmb#1{\ket{\pmb{#1}}}
H^{\otimes n}\ketpmb{k} = \frac{1}{\sqrt{N}}\sum_{\pmb{j}=\pmb0}^{\pmb{N-1}} (-1)^{\pmb{j}\odot\pmb{k}}\ketpmb{j}
" />

Substituting this expression in our earlier state we get,

<img src="https://i.upmath.me/svg/%5Cdef%5Cket%231%7B%5Cleft%7C%5Censuremath%7B%231%7D%5Cright%5Crangle%7D%0A%5Cdef%5Cketpmb%231%7B%5Cket%7B%5Cpmb%7B%231%7D%7D%7D%0A%5Cbegin%7Balign*%7D%0AH%5E%7B%5Cotimes%20n%7D%5Cfrac%7B1%7D%7B%5Csqrt2%7D%5Cbig(%5Cketpmb%7B%5Ctilde%7Bx%7D%7D%2B%5Cket%7B%5Cpmb%7B%5Ctilde%7Bx%7D%7D%5Coplus%5Cpmb%7Bs%7D%7D%5Cbig)%20%26%3D%20%5Cfrac%7B1%7D%7B%5Csqrt%7B2N%7D%7D%5Csum_%7B%5Cpmb%7Bj%7D%7D%20%5Cleft%5B%20(-1)%5E%7B%5Cpmb%7Bj%7D%5Codot%5Cpmb%7B%5Ctilde%7Bx%7D%7D%7D%20%2B%20(-1)%5E%7B%5Cpmb%7Bj%7D%5Codot(%5Cpmb%7B%5Ctilde%7Bx%7D%7D%5Coplus%5Cpmb%7Bs%7D)%7D%20%5Cright%5D%5Cket%7B%5Cpmb%7Bj%7D%7D%5C%5C%0A%26%3D%20%5Cfrac%7B1%7D%7B%5Csqrt%7B2N%7D%7D%5Csum_%7B%5Cpmb%7Bj%7D%7D(-1)%5E%7B%5Cpmb%7Bj%7D%5Codot%5Cpmb%7B%5Ctilde%7Bx%7D%7D%7D%20%5Cleft%5B%20%0A1%20%2B%20(-1)%5E%7B%5Cpmb%7Bj%7D%5Codot%5Cpmb%7Bs%7D%7D%20%5Cright%5D%20%5Cketpmb%7Bj%7D%0A%5Cend%7Balign*%7D%0A" alt="\def\ket#1{\left|\ensuremath{#1}\right\rangle}
\def\ketpmb#1{\ket{\pmb{#1}}}
\begin{align*}
H^{\otimes n}\frac{1}{\sqrt2}\big(\ketpmb{\tilde{x}}+\ket{\pmb{\tilde{x}}\oplus\pmb{s}}\big) &amp;= \frac{1}{\sqrt{2N}}\sum_{\pmb{j}} \left[ (-1)^{\pmb{j}\odot\pmb{\tilde{x}}} + (-1)^{\pmb{j}\odot(\pmb{\tilde{x}}\oplus\pmb{s})} \right]\ket{\pmb{j}}\\
&amp;= \frac{1}{\sqrt{2N}}\sum_{\pmb{j}}(-1)^{\pmb{j}\odot\pmb{\tilde{x}}} \left[ 
1 + (-1)^{\pmb{j}\odot\pmb{s}} \right] \ketpmb{j}
\end{align*}
" />

Since <img src="https://i.upmath.me/svg/%5Cpmb%7Bs%7D%5Cneq%5Cpmb0" alt="\pmb{s}\neq\pmb0" /> for the case of two-to-one function, for <img src="https://i.upmath.me/svg/%5Ctfrac%7BN%7D%7B2%7D" alt="\tfrac{N}{2}" /> values of <img src="https://i.upmath.me/svg/%5Cpmb%7Bj%7D" alt="\pmb{j}" /> we will get <img src="https://i.upmath.me/svg/%5Cpmb%7Bj%7D%5Codot%5Cpmb%7Bs%7D%3D0" alt="\pmb{j}\odot\pmb{s}=0" /> and for the rest we will get <img src="https://i.upmath.me/svg/%5Cpmb%7Bj%7D%5Codot%5Cpmb%7Bs%7D%3D1" alt="\pmb{j}\odot\pmb{s}=1" />. For the later case, the coefficient of <img src="https://i.upmath.me/svg/%7C%5Cpmb%7Bj%7D%5Crangle" alt="|\pmb{j}\rangle" /> will vanish, because <img src="https://i.upmath.me/svg/%5B1%2B(-1)%5E1%5D%3D0" alt="[1+(-1)^1]=0" />. And only the states for which <img src="https://i.upmath.me/svg/%5Cpmb%7Bj%7D%5Codot%5Cpmb%7Bs%7D%3D0" alt="\pmb{j}\odot\pmb{s}=0" /> will survive, because <img src="https://i.upmath.me/svg/%5B1%2B(-1)%5E0%5D%3D2" alt="[1+(-1)^0]=2" />. So the state can be written as,

<img src="https://i.upmath.me/svg/%5Cdef%5Cket%231%7B%5Cleft%7C%5Censuremath%7B%231%7D%5Cright%5Crangle%7D%0A%5Cdef%5Cketpmb%231%7B%5Cket%7B%5Cpmb%7B%231%7D%7D%7D%0AH%5E%7B%5Cotimes%20n%7D%5Cfrac%7B1%7D%7B%5Csqrt2%7D%5Cbig(%5Cketpmb%7B%5Ctilde%7Bx%7D%7D%2B%5Cket%7B%5Cpmb%7B%5Ctilde%7Bx%7D%7D%5Coplus%5Cpmb%7Bs%7D%7D%5Cbig)%20%3D%20%0A%5Csqrt%7B%5Cfrac%7B2%7D%7BN%7D%7D%5Csum_%7B%5Csubstack%7B%5Cpmb%7Bj%7D%5C%5C%5Cpmb%7Bj%7D%5Codot%5Cpmb%7Bs%7D%3D0%7D%7D(-1)%5E%7B%5Cpmb%7Bj%7D%5Codot%5Cpmb%7B%5Ctilde%7Bx%7D%7D%7D%5Cketpmb%7Bj%7D%0A" alt="\def\ket#1{\left|\ensuremath{#1}\right\rangle}
\def\ketpmb#1{\ket{\pmb{#1}}}
H^{\otimes n}\frac{1}{\sqrt2}\big(\ketpmb{\tilde{x}}+\ket{\pmb{\tilde{x}}\oplus\pmb{s}}\big) = 
\sqrt{\frac{2}{N}}\sum_{\substack{\pmb{j}\\\pmb{j}\odot\pmb{s}=0}}(-1)^{\pmb{j}\odot\pmb{\tilde{x}}}\ketpmb{j}
" />

Next, we do a measurement on the input register. So, after measurement we get a random state <img src="https://i.upmath.me/svg/%7C%5Cpmb%7Bj%7D%5Crangle" alt="|\pmb{j}\rangle" /> for which it is true that <img src="https://i.upmath.me/svg/%5Cpmb%7Bj%7D%5Codot%5Cpmb%7Bs%7D%3D0%7D" alt="\pmb{j}\odot\pmb{s}=0}" />. Lastly, we repeat this circuit multiple times and perform classical post-processing on it.

#### One-to-One Case
If the function was one-to-one, then <img src="https://i.upmath.me/svg/%5Cpmb%7Bs%7D%3D%5Cpmb%7B0%7D" alt="\pmb{s}=\pmb{0}" /> and <img src="https://i.upmath.me/svg/%5Cleft%7C%5Cpmb%7Bx%7D%5Cright%5Crangle%20%3D%20%5Cleft%7C%5Cpmb%7Bx%7D%5Coplus%5Cpmb%7Bs%7D%5Cright%5Crangle" alt="\left|\pmb{x}\right\rangle = \left|\pmb{x}\oplus\pmb{s}\right\rangle" />. So after measurement of the output qubit, we will get a system of the form
<img src="https://i.upmath.me/svg/%5Cdef%5Cket%231%7B%5Cleft%7C%5Censuremath%7B%231%7D%5Cright%5Crangle%7D%0A%5Cdef%5Cketpmb%231%7B%5Cket%7B%5Cpmb%7B%5Ctilde%7B%231%7D%7D%7D%7D%0A%5Cketpmb%7Bx%7D%5Cket%7Bf(%5Cpmb%7B%5Ctilde%7Bx%7D%7D)%7D%0A" alt="\def\ket#1{\left|\ensuremath{#1}\right\rangle}
\def\ketpmb#1{\ket{\pmb{\tilde{#1}}}}
\ketpmb{x}\ket{f(\pmb{\tilde{x}})}
" />. After the final Hadamard gates a similar calculation will apply,

<img src="https://i.upmath.me/svg/%5Cdef%5Cket%231%7B%5Cleft%7C%5Censuremath%7B%231%7D%5Cright%5Crangle%7D%0A%5Cdef%5Cketpmb%231%7B%5Cket%7B%5Cpmb%7B%231%7D%7D%7D%0AH%5E%7B%5Cotimes%20n%7D%5Cketpmb%7B%5Ctilde%7Bx%7D%7D%20%3D%20%5Cfrac%7B1%7D%7B%5Csqrt%7BN%7D%7D%5Csum_%7B%5Cpmb%7Bj%7D%3D%5Cpmb0%7D%5E%7B%5Cpmb%7BN-1%7D%7D%20(-1)%5E%7B%5Cpmb%7Bj%7D%5Codot%5Cpmb%7B%5Ctilde%7Bx%7D%7D%7D%5Cketpmb%7Bj%7D%0A" alt="\def\ket#1{\left|\ensuremath{#1}\right\rangle}
\def\ketpmb#1{\ket{\pmb{#1}}}
H^{\otimes n}\ketpmb{\tilde{x}} = \frac{1}{\sqrt{N}}\sum_{\pmb{j}=\pmb0}^{\pmb{N-1}} (-1)^{\pmb{j}\odot\pmb{\tilde{x}}}\ketpmb{j}
" />

Since this state is an equal superposition of all possible input states (with/without a phase), if  we do a measurement, we have an equal chance of getting any of the input states. Although the calculation path for the one-to-one case was a bit different from the two-to-one case, the fact that we measure only states <img src="https://i.upmath.me/svg/%7C%5Cpmb%7Bj%7D%5Crangle" alt="|\pmb{j}\rangle" /> where <img src="https://i.upmath.me/svg/%5Cpmb%7Bj%7D%5Codot%5Cpmb%7Bs%7D%3D0" alt="\pmb{j}\odot\pmb{s}=0" /> remains true (becuase <img src="https://i.upmath.me/svg/%5Cpmb%7Bs%7D%3D%5Cpmb%7B0%7D" alt="\pmb{s}=\pmb{0}" />).

### Classical Post-processing
After executing the quantum circuit <img src="https://i.upmath.me/svg/n" alt="n" /> times, we will have a set of <img src="https://i.upmath.me/svg/%5Cpmb%7Bj%7D" alt="\pmb{j}" />'s for which <img src="https://i.upmath.me/svg/%5Cpmb%7Bj%7D%5Codot%5Cpmb%7Bs%7D%3D0" alt="\pmb{j}\odot\pmb{s}=0" />. Let us label them as <img src="https://i.upmath.me/svg/%5Cpmb%7Bj_1%7D%2C%5Cpmb%7Bj_2%7D%2C%5Cpmb%7Bj_3%7D%2C%5Cdots%2C%5Cpmb%7Bj_n%7D" alt="\pmb{j_1},\pmb{j_2},\pmb{j_3},\dots,\pmb{j_n}" />. Using them we can set a system of <img src="https://i.upmath.me/svg/n" alt="n" /> linear equations to find all the unknown bits of <img src="https://i.upmath.me/svg/%5Cpmb%7Bs%7D" alt="\pmb{s}" />.

<img src="https://i.upmath.me/svg/%5Cbegin%7Balign*%7D%0A%5Cpmb%7Bj_1%7D%5Codot%5Cpmb%7Bs%7D%26%3Dj_%7B1_0%7Ds_0%5Coplus%20j_%7B1_1%7Ds_1%5Coplus%5Cdots%5Coplus%20j_%7B1_%7Bn-1%7D%7Ds_%7Bn-1%7D%26%3D0%5C%5C%0A%5Cpmb%7Bj_2%7D%5Codot%5Cpmb%7Bs%7D%26%3Dj_%7B2_0%7Ds_0%5Coplus%20j_%7B2_1%7Ds_1%5Coplus%5Cdots%5Coplus%20j_%7B2_%7Bn-1%7D%7Ds_%7Bn-1%7D%26%3D0%5C%5C%0A%5Cpmb%7Bj_3%7D%5Codot%5Cpmb%7Bs%7D%26%3Dj_%7B3_0%7Ds_0%5Coplus%20j_%7B3_1%7Ds_1%5Coplus%5Cdots%5Coplus%20j_%7B3_%7Bn-1%7D%7Ds_%7Bn-1%7D%26%3D0%5C%5C%0A%26%5Cqquad%5Cdots%26%5Cdots%5C%5C%0A%5Cpmb%7Bj_n%7D%5Codot%5Cpmb%7Bs%7D%26%3Dj_%7Bn_0%7Ds_0%5Coplus%20j_%7Bn_1%7Ds_1%5Coplus%5Cdots%5Coplus%20j_%7Bn_%7Bn-1%7D%7Ds_%7Bn-1%7D%26%3D0%0A%5Cend%7Balign*%7D" alt="\begin{align*}
\pmb{j_1}\odot\pmb{s}&amp;=j_{1_0}s_0\oplus j_{1_1}s_1\oplus\dots\oplus j_{1_{n-1}}s_{n-1}&amp;=0\\
\pmb{j_2}\odot\pmb{s}&amp;=j_{2_0}s_0\oplus j_{2_1}s_1\oplus\dots\oplus j_{2_{n-1}}s_{n-1}&amp;=0\\
\pmb{j_3}\odot\pmb{s}&amp;=j_{3_0}s_0\oplus j_{3_1}s_1\oplus\dots\oplus j_{3_{n-1}}s_{n-1}&amp;=0\\
&amp;\qquad\dots&amp;\dots\\
\pmb{j_n}\odot\pmb{s}&amp;=j_{n_0}s_0\oplus j_{n_1}s_1\oplus\dots\oplus j_{n_{n-1}}s_{n-1}&amp;=0
\end{align*}" />

If these equations are linearly independent, then we can successfully find <img src="https://i.upmath.me/svg/%5Cpmb%7Bs%7D" alt="\pmb{s}" />. In fact, it is more likely that they are linearly dependent. But how likely is it? And can we still achieve a set of linearly independent set of equations? Actually, yes. 

Since <img src="https://i.upmath.me/svg/%5Cpmb%7Bj_1%7D%2C%5Cpmb%7Bj_2%7D%2C%5Cpmb%7Bj_3%7D%2C%5Cdots%2C%5Cpmb%7Bj_n%7D" alt="\pmb{j_1},\pmb{j_2},\pmb{j_3},\dots,\pmb{j_n}" /> are just binary strings, it is relatively easy to check if they are linearly independent or not. For example, <img src="https://i.upmath.me/svg/%5C%7B%5Cpmb%7Bj_1%7D%2C%5Cpmb%7Bj_2%7D%5C%7D" alt="\{\pmb{j_1},\pmb{j_2}\}" /> can only be linearly dependent if they are equal. Then the likelihood of <img src="https://i.upmath.me/svg/%5Cpmb%7Bj_2%7D" alt="\pmb{j_2}" /> being linearly dependent from <img src="https://i.upmath.me/svg/%5Cpmb%7Bj_1%7D" alt="\pmb{j_1}" /> is then <img src="https://i.upmath.me/svg/%5Ctfrac%7B1%7D%7B2%5En%7D" alt="\tfrac{1}{2^n}" />. Next, <img src="https://i.upmath.me/svg/%5C%7B%5Cpmb%7Bj_1%7D%2C%5Cpmb%7Bj_2%7D%2C%5Cpmb%7Bj_3%7D%5C%7D" alt="\{\pmb{j_1},\pmb{j_2},\pmb{j_3}\}" /> is linearly dependent iff <img src="https://i.upmath.me/svg/%5Cpmb%7Bj_3%7D%3D%5Cpmb%7Bj_1%7D" alt="\pmb{j_3}=\pmb{j_1}" /> or <img src="https://i.upmath.me/svg/%5Cpmb%7Bj_3%7D%3D%5Cpmb%7Bj_2%7D" alt="\pmb{j_3}=\pmb{j_2}" /> or <img src="https://i.upmath.me/svg/%5Cpmb%7Bj_3%7D%3D%5Cpmb%7Bj_1%7D%5Coplus%5Cpmb%7Bj_2%7D" alt="\pmb{j_3}=\pmb{j_1}\oplus\pmb{j_2}" />. So the probability of <img src="https://i.upmath.me/svg/%5Cpmb%7Bj_3%7D" alt="\pmb{j_3}" /> being linearly dependent on previous equations will be <img src="https://i.upmath.me/svg/%5Ctfrac%7B3%7D%7B2%5En%7D" alt="\tfrac{3}{2^n}" />.  Similarly <img src="https://i.upmath.me/svg/%5Cpmb%7Bj_4%7D" alt="\pmb{j_4}" /> will be linearly dependent if <img src="https://i.upmath.me/svg/%5Cpmb%7Bj_4%7D%3D%5Calpha_1%5Cpmb%7Bj_1%7D%2B%5Calpha_2%5Cpmb%7Bj_2%7D%2B%5Calpha_3%5Cpmb%7Bj_3%7D" alt="\pmb{j_4}=\alpha_1\pmb{j_1}+\alpha_2\pmb{j_2}+\alpha_3\pmb{j_3}" /> where <img src="https://i.upmath.me/svg/%5Calpha_i%3D1" alt="\alpha_i=1" /> for any <img src="https://i.upmath.me/svg/i%5Cin%5B1%2C3%5D" alt="i\in[1,3]" />. Since there <img src="https://i.upmath.me/svg/7" alt="7" /> possibilities, the probability will be <img src="https://i.upmath.me/svg/%5Ctfrac%7B7%7D%7B2%5En%7D" alt="\tfrac{7}{2^n}" />.

In general, the <img src="https://i.upmath.me/svg/k" alt="k" />-th equation will be linearly dependent from all previous equations, iff <img src="https://i.upmath.me/svg/%5Cpmb%7Bj_k%7D%3D%5Csum_%7Bi%3D1%7D%5E%7Bk-1%7D%5Calpha_i%5Cpmb%7Bj_i%7D" alt="\pmb{j_k}=\sum_{i=1}^{k-1}\alpha_i\pmb{j_i}" /> where <img src="https://i.upmath.me/svg/%5Calpha_i%3D1" alt="\alpha_i=1" /> for any <img src="https://i.upmath.me/svg/i%5Cin%5B1%2Ck)" alt="i\in[1,k)" />. The likelihood of being so is then,

<img src="https://i.upmath.me/svg/%5CPr(%5Ctext%7B%24k%24-th%20equation%20is%20linearly%20dependent%7D)%20%3D%20%5Cfrac%7B2%5E%7Bk-1%7D-1%7D%7B2%5En%7D" alt="\Pr(\text{$k$-th equation is linearly dependent}) = \frac{2^{k-1}-1}{2^n}" /> 

Then the probability that all <img src="https://i.upmath.me/svg/n" alt="n" /> equations are linearly independent is,

<img src="https://i.upmath.me/svg/%5CPr(%5Ctext%7Ball%20%24n%24%20equations%20are%20linearly%20independent%7D)%20%3D%20%5Cprod_%7Bk%3D1%7D%5E%7Bn%7D%5Cleft(1-%5Cfrac%7B2%5E%7Bk-1%7D-1%7D%7B2%5En%7D%5Cright)" alt="\Pr(\text{all $n$ equations are linearly independent}) = \prod_{k=1}^{n}\left(1-\frac{2^{k-1}-1}{2^n}\right)" />

Even though it is difficult to get an exact formula for the probability, we can easily find a lower bound,

<img src="https://i.upmath.me/svg/%5Cbegin%7Balign*%7D%0A%5Cprod_%7Bk%3D1%7D%5E%7Bn%7D%5Cleft(1-%5Cfrac%7B2%5E%7Bk-1%7D-1%7D%7B2%5En%7D%5Cright)%20%0A%26%3D%20%5Cprod_%7Bk%3D1%7D%5E%7Bn%7D%5Cleft(1-%5Cfrac%7B2%5E%7Bk-1%7D%7D%7B2%5En%7D%2B%5Cfrac%7B1%7D%7B2%5En%7D%5Cright)%5C%5C%0A%26%5Cgeq%20%5Cprod_%7Bk%3D1%7D%5E%7Bn%7D%5Cleft(1-%5Cfrac%7B2%5E%7Bk-1%7D%7D%7B2%5En%7D%5Cright)%5C%5C%0A%26%3D%20%5Cprod_%7Bk%3D1%7D%5E%7Bn%7D%5Cleft(1-%5Cfrac%7B1%7D%7B2%5E%7Bn-k%2B1%7D%7D%5Cright)%5C%5C%0A%26%3D%20%5Cprod_%7Bl%3D1%7D%5E%7Bn%7D%5Cleft(1-%5Cfrac%7B1%7D%7B2%5E%7Bl%7D%7D%5Cright)%5C%5C%0A%26%5Cgeq%20%5Cprod_%7Bl%3D1%7D%5E%7B%5Cinfty%7D%5Cleft(1-2%5E%7B-l%7D%5Cright)%5C%5C%0A%26%5Capprox%200.288788...%20%5Cgeq%200.25%0A%5Cend%7Balign*%7D" alt="\begin{align*}
\prod_{k=1}^{n}\left(1-\frac{2^{k-1}-1}{2^n}\right) 
&amp;= \prod_{k=1}^{n}\left(1-\frac{2^{k-1}}{2^n}+\frac{1}{2^n}\right)\\
&amp;\geq \prod_{k=1}^{n}\left(1-\frac{2^{k-1}}{2^n}\right)\\
&amp;= \prod_{k=1}^{n}\left(1-\frac{1}{2^{n-k+1}}\right)\\
&amp;= \prod_{l=1}^{n}\left(1-\frac{1}{2^{l}}\right)\\
&amp;\geq \prod_{l=1}^{\infty}\left(1-2^{-l}\right)\\
&amp;\approx 0.288788... \geq 0.25
\end{align*}" />

Although it is quite difficult to evaluate the infinite product, it is much easier to prove using induction that the value is at least 0.25. In fact to prove this, let us prove a stronger statement: <img src="https://i.upmath.me/svg/%5Cprod_%7Bl%3D1%7D%5E%7Bn%7D%5Cleft(1-2%5E%7B-l%7D%5Cright)%5Cgeq0.25%2B2%5E%7B-(n%2B1)%7D" alt="\prod_{l=1}^{n}\left(1-2^{-l}\right)\geq0.25+2^{-(n+1)}" />. The base case <img src="https://i.upmath.me/svg/n%3D1" alt="n=1" /> is trivially true. For the inductive step, we have to show that,

<img src="https://i.upmath.me/svg/%5Cbegin%7Balign*%7D%0A%25%5Cprod_%7Bl%3D1%7D%5E%7Bn%2B1%7D%5Cleft(1-2%5E%7B-l%7D%5Cright)%20%26%5Cgeq%200.25%20%2B%202%5E%7B-(n%2B2)%7D%5C%5C%0A%25or%2C%20%5Cleft(1-2%5E%7B-(n%2B1)%7D%5Cright)%5Cprod_%7Bl%3D1%7D%5E%7Bn%7D%5Cleft(1-2%5E%7B-l%7D%5Cright)%0A%25%26%5Cgeq%200.25%20%2B%202%5E%7B-(n%2B2)%7D%5C%5C%0A%26%5Cleft(0.25%2B2%5E%7B-(n%2B1)%7D%5Cright)%5Cleft(1-2%5E%7B-(n%2B1)%7D%5Cright)%0A%5Cgeq%200.25%20%2B%202%5E%7B-(n%2B2)%7D%5C%5C%0A%5Cimplies%26%200.25%2B2%5E%7B-(n%2B1)%7D-0.25%5Ccdot2%5E%7B-(n%2B1)%7D-2%5E%7B-2(n%2B1)%7D%0A%5Cgeq%200.25%20%2B%202%5E%7B-(n%2B2)%7D%5C%5C%0A%5Cimplies%26%200.25%5Ccdot2%5E%7B-(n%2B1)%7D%20%5Cgeq%202%5E%7B-2(n%2B1)%7D%5C%5C%0A%5Cimplies%26%202%5E%7B(n%2B1)%7D%5Cgeq%204%5C%5C%0A%5Cend%7Balign*%7D" alt="\begin{align*}
%\prod_{l=1}^{n+1}\left(1-2^{-l}\right) &amp;\geq 0.25 + 2^{-(n+2)}\\
%or, \left(1-2^{-(n+1)}\right)\prod_{l=1}^{n}\left(1-2^{-l}\right)
%&amp;\geq 0.25 + 2^{-(n+2)}\\
&amp;\left(0.25+2^{-(n+1)}\right)\left(1-2^{-(n+1)}\right)
\geq 0.25 + 2^{-(n+2)}\\
\implies&amp; 0.25+2^{-(n+1)}-0.25\cdot2^{-(n+1)}-2^{-2(n+1)}
\geq 0.25 + 2^{-(n+2)}\\
\implies&amp; 0.25\cdot2^{-(n+1)} \geq 2^{-2(n+1)}\\
\implies&amp; 2^{(n+1)}\geq 4\\
\end{align*}" />

Since <img src="https://i.upmath.me/svg/n%5Cgeq1" alt="n\geq1" />, the inequality always holds true, proving the inductive hypothesis. When <img src="https://i.upmath.me/svg/n%5Cto%5Cinfty" alt="n\to\infty" />, in the limit, the product remains always greater than 0.25.

Now that we have shown that the probability of all <img src="https://i.upmath.me/svg/n" alt="n" /> equations being linearly independent is at least 25% for any <img src="https://i.upmath.me/svg/n" alt="n" />, we can repeat this scheme a constant amount of time to get a higher probability. For example, if we run the scheme 10 times, the probability at least get one set of linearly independent equations is, <img src="https://i.upmath.me/svg/1-(1-0.25)%5E10%5Capprox94.37%5C%25" alt="1-(1-0.25)^10\approx94.37\%" />. And if necessary, we can achieve even higher accuracy without increasing the query complexity <img src="https://i.upmath.me/svg/%5Cmathcal%7BO%7D(n)" alt="\mathcal{O}(n)" />.
## Conclusion
Even though we require post-processing, we only queried <img src="https://i.upmath.me/svg/%5Cmathcal%7BO%7D(n)" alt="\mathcal{O}(n)" /> times in the quantum subroutine. So this is our query complexity. However, it should be noted that this is not the time complexity since solving the linear equations will add more complexity. Compared to this, in the classical approach, we required a query complexity of <img src="https://i.upmath.me/svg/%5COmega(%5Csqrt%7B2%5En%7D)" alt="\Omega(\sqrt{2^n})" />. Thus we get an exponential speedup over the classical approach. It should be noted that the inherent symmetry in the Simon's problem is what allowed us to gain exponential quantum speedup. A similar but without any hidden structure is the collision problem. Even though there is a quantum algorithm (Grover's) that gives us a speedup, it is only polynomial. The Deutsch-Jozsa problem is sometimes said to show exponential quantum speedup. However, this exponential speedup is only over deterministic algorithms. There exists randomized classical algorithm that solves Deutsch-Jozsa problem in <img src="https://i.upmath.me/svg/%5Cmathcal%7BO%7D(1)" alt="\mathcal{O}(1)" /> queries. Thus Simon's algorithm demonstrates without a doubt that there exists quantum algorithms that can solve some problems in polynomial queries whereas classical algorithms require exponential queries.
## Notation
* <img src="https://i.upmath.me/svg/%5Cmathbb%7BB%7D" alt="\mathbb{B}" />: the set of binary digits i.e. <img src="https://i.upmath.me/svg/%5C%7B0%2C1%5C%7D" alt="\{0,1\}" />.
* <img src="https://i.upmath.me/svg/%5Cmathbb%7BB%7D%5En" alt="\mathbb{B}^n" />: the set of all possible <img src="https://i.upmath.me/svg/n" alt="n" />-bit binary strings.
* <img src="https://i.upmath.me/svg/%5Cpmb%7Bx%7D" alt="\pmb{x}" />: always bold; an element of <img src="https://i.upmath.me/svg/%5Cmathbb%7BB%7D%5Ek" alt="\mathbb{B}^k" /> and so represents a binary string.
* <img src="https://i.upmath.me/svg/%5Coplus" alt="\oplus" />: bitwise xor (mod 2 addition) operation that outputs another binary string.
* <img src="https://i.upmath.me/svg/%5Codot" alt="\odot" />: binary dot product; if <img src="https://i.upmath.me/svg/%5Cpmb%7Bx%7D%2C%5Cpmb%7By%7D%5Cin%5Cmathbb%7BB%7D%5Ek" alt="\pmb{x},\pmb{y}\in\mathbb{B}^k" /> then <img src="https://i.upmath.me/svg/%5Cpmb%7Bx%7D%5Codot%5Cpmb%7By%7D%20%3D%20x_%7Bk-1%7Dy_%7Bk-1%7D%20%5Coplus%20%5Cdots%20%5Coplus%20x_1y_1%20%5Coplus%20x_0y_0" alt="\pmb{x}\odot\pmb{y} = x_{k-1}y_{k-1} \oplus \dots \oplus x_1y_1 \oplus x_0y_0" />.
<!--* <img src="https://i.upmath.me/svg/%5Cpmb%7B7%7D" alt="\pmb{7}" />: numbers but bold; represent binary vector <img src="https://i.upmath.me/svg/(1%2C1%2C1)" alt="(1,1,1)" /> expressed as a decimal number.-->

[1]: https://youtu.be/6qD9XElTpCE?si=7gxFxYWtNAZ_qbh4&t=660
[2]: https://aws.amazon.com/blogs/quantum-computing/simons-algorithm/
[3]: https://www.desmos.com/3d/bohm0onfec
