# A Note On Simon's Algorithm

Simon's algorithm, introduced by Daniel Simon in 1994, was the first quantum algorithm to truly demonstrate an exponential speedup over classical computation. While earlier quantum algorithms, such as Deutsch-Jozsa and Bernstein-Vazirani, showcased quantum parallelism, they only achieved a polynomial advantage over randomized classical algorithms. Simon’s result was groundbreaking because it provided the first provable separation between quantum and classical complexity in a computational problem. This discovery shattered previous assumptions about the limits of classical computation and provided strong evidence that quantum computers could fundamentally outperform their classical counterparts for certain tasks.

### Historical Background
There is an interesting story<sup>[[1]][[2]]</sup> behind Simon’s work. In 1993, Simon initially submitted his paper to STOC (ACM Symposium on the Theory of Computing), one of the two most prestigious conferences in theoretical computer science (alongside FOCS, the IEEE Symposium on Foundations of Computer Science). However, the paper was rejected, as many in the classical computing community remained skeptical of quantum computing's significance.

One of the members of the STOC 1993 program committee was Peter Shor, who immediately recognized the importance of Simon’s result. Although he could not convince the rest of the committee to accept the paper, he was deeply inspired by the idea. Shor realized that he could extend Simon’s approach to solve the discrete logarithm problem, which underpins many cryptographic protocols. This insight led him to develop his quantum algorithm for integer factorization, now known as Shor’s algorithm.

After developing his algorithm, Shor personally requested Simon to submit both of their papers together to FOCS. Simon resubmitted his work, and this time, it was accepted alongside Shor’s paper. Both papers were published together in FOCS 1994, marking a pivotal moment in quantum computing. Simon’s result provided the first exponential separation between quantum and classical complexity, while Shor’s algorithm demonstrated the real-world impact of quantum computation by breaking widely used cryptographic schemes. Their publication in one of the most prestigious theoretical computer science conferences firmly established quantum algorithms as a transformative field of research.
## Problem Description
The simpler (non-decision) version of the Simon's problem considers a two-to-one function $$f:\mathbb{B}^n\to\mathbb{B}^m$$ that takes in an $$n$$-bit binary string and spits out an $$m$$-bit binary string, where $$m\geq n-1$$. The function is promised to be a balanced two-to-one function such that for $$f(\pmb{x_1})=f(\pmb{x_2})$$ if and only if $$\pmb{x_2}=\pmb{x_1}\oplus\pmb{s}$$ for some hidden binary string $$\pmb{s}\in\mathbb{B}^n}$$. 

In other words, $$f(\pmb{x})=f(\pmb{x}\oplus\pmb{s})$$ and this output is unique only for that specific $$\pmb{x}$$ and $$\pmb{x}\oplus\pmb{s}$$. However, we do not know the value of $$\pmb{s}$$ neither do we have access to the inner workings of the function $$f(\pmb{x})$$ (i.e. oracle/black box model). The goal is to develop an algorithm to determine the binary string $$\pmb{s}$$.

The original (decision) version of the Simon's problem also considers the case where $$f(\pmb{x})$$ is a one-to-one function. This natural extension does not change the promise and only happens when $$\pmb{s}=\pmb{0}$$. Since the function can now either be a one-to-one or a two-to-one function, $$m$$ is at least $$n$$.

### Problem Statement
> * **Oracle:** A function $$f:\mathbb{B}^n\to\mathbb{B}^m$$ where $$m\geq n$$.
> * **Promise:** $$f(\pmb{x_1})=f(\pmb{x_2})\iff\pmb{x_2}=\pmb{x_1}\oplus\pmb{s}$$
> * **Problem:** Find $$\pmb{s}$$.
### Example
Let's look at a specific example where $$m=n=3, \pmb{s}=011_2=\pmb3$$ and the two-to-one function $$f(\pmb{x})=\max(\pmb{x},\pmb{x}\oplus\pmb{s})$$. Please note that here max is just typical maximum of two numbers taking $$\pmb{x}$$ as a binary number. Often times for simplicity, we will represent a binary string by just the decimal representation (e.g. $$011_2$$ as $$\pmb3$$). Below is a table evaluated for all possible $$\pmb{x}$$ (both in binary and decimal form):

$$
\begin{tabular}{ccc|c c c|c}
$\pmb{s}$ & $\pmb{x}$ & $\pmb{x}\oplus\pmb{s}$ & $\max(\pmb{x},\pmb{x}\oplus\pmb{s})$ & \phantom{huge gap} & $\pmb{x}$ & $f(\pmb{x})$\\
\cline{1-4}\cline{6-7}
    & 000 & 011 & 011 && \pmb0 & \pmb3\\
    & 001 & 010 & 010 && \pmb1 & \pmb2\\
    & 010 & 001 & 010 && \pmb2 & \pmb2\\
011 & 011 & 000 & 011 && \pmb3 & \pmb3\\
    & 100 & 111 & 111 && \pmb4 & \pmb7\\
    & 101 & 110 & 110 && \pmb5 & \pmb6\\
    & 110 & 101 & 110 && \pmb6 & \pmb6\\
    & 111 & 100 & 111 && \pmb7 & \pmb7\\
\end{tabular}
$$

### Visualization
We can represent each input $$\pmb{x}$$ with a node and connecting them with an edge if their outputs match. For better visualization, we can paint them with the same color to denote they are related. If the oracle is a two-to-one function, every node will be connected exactly one other node<sup>[[3]]</sup>. However, if the oracle is a one-to-one function, all the nodes will be disjoint.

For our specific example, we can represent all the input using the vertices of a (Hamming) cube. Since $$f(\pmb0)=f(\pmb3)$$, both of them are of same color and are connected by a line. From the diagram, it is easy to see there is a certain symmetry in Simon's problem:

$$\begin{tikzpicture}[scale=2]
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
\end{tikzpicture}$$

### General visualization

If $$f(\pmb{x})$$ is two-to-one:

$$
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
$$

If $$f(\pmb{x})$$ is one-to-one:

$$
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
$$


## Classical Approach
One naive approach might be to evaluate $$f(\pmb0)$$ and keep querying until we find an $$\pmb{x}$$ for which we get $$f(\pmb{x})=f(\pmb0)$$. Then, from the promise, $$\pmb{x}=\pmb0\oplus\pmb{s}=\pmb{s}$$. However, in the worst case scenario, we will have to query all possible inputs until we get a match. So a total of $$2^{n}-1$$ times. On average, this will get improved to $$2^{n-1}$$ queries.

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

Say, we have queried the function $$(k-1)$$ times without any collision. If $$f(\pmb{x})$$ is a two-to-one function, even though $$N=2^n$$ different inputs are possible, there are only $$M=\tfrac{N}{2}=2^{n-1}$$ unique outputs. In the $$k$$-th iteration, the probability of getting a collision is: 

$$\Pr(\text{collision on the $k$-th iteration})=\frac{k-1}{2^{n-1}}=\frac{k-1}{N/2}$$

So, the probability of getting no collision with any output from the first iteration to $$(k-1)$$-th iteration is:

$$\Pr(\text{no collision after $k$ iterations})=\prod_{j=0}^{k-1}\left(1-\frac{2j}{N}\right)$$

Taking logarithm on both sides and approximating $$\ln(1+x)\lessapprox x$$ when $$x\to0$$, we get,

$$\ln(\Pr(\text{no collision})) = \sum_{j=0}^{k-1}\ln\left(1-\frac{2j}{N}\right) \lessapprox \sum_{j=0}^{k-1}-\frac{2j}{N}$$

Since $$\sum_{j=0}^{k-1}j=\frac{k(k-1)}{2}\gtrapprox\frac{k^2}{2}$$, we have, 

$$\ln(\Pr(\text{no collision})) \lessapprox -\frac{2}{N}\sum_{j=0}^{k-1}j \lessapprox -\frac{k^2}{N}$$

$$\therefore \Pr(\text{no collision}) \lessapprox \exp\left(-\frac{k^2}{N}\right)$$

Since our algorithm will stop once it finds a collision, it is reassuring that we have an exponential decay function here. It means that, the more we query (i.e. $$k$$ increases), chance of not colliding decreases exponentially (i.e. $$\Pr(\text{collision})$$ increases). To achieve a probability of (say) $$90\%$$ of collision, we need to query $$k$$ times where,

$$\Pr(\text{no collision})=1-0.9=0.1\lessapprox \exp\left(-\frac{k^2}{N}\right)$$

$$\implies k\gtrapprox\sqrt{-N\ln0.1}=\sqrt{N\ln10}$$

$$\therefore k = \Omega\left(\sqrt{N}\right) = \Omega\left(\sqrt{2^n}\right)$$

In conclusion, to ensure a high probabiliy of collision i.e. successfully stopping the program, we need $$\Omega\left(\sqrt{2^n}\right)$$ queries.

## Simon's Algorithm
Simon's algorithm consists of two parts: quantum subroutine and classical post-processing. The following quantum circuit is executed $$\mathcal{O}(n)$$ times and then the measured data is used for the post-processing part. There we have to solve a system of linear equations to finally get $$\pmb{s}$$.

![Quantum Circuit for Simon's Algorithm](https://www.researchgate.net/publication/350180612/figure/fig12/AS:1003308181381161@1616218719487/The-quantum-circuit-of-Simons-algorithm-The-measurement-in-the-dotted-box-could-be.png)
### Quantum Subroutine
The first $$n$$-qubit register is the input register and the last $$m$$-qubit register is the output register. After passing through the first Hadamard gate, we will have an equal superposition of all $$N=2^n$$ input states,

$$\def\ket#1{\left|\ensuremath{#1}\right\rangle}
\def\ketpmb#1{\ket{\pmb{#1}}}
H^{\otimes n}\ketpmb{0} = \frac{1}{\sqrt{N}}\sum_{\pmb{x}=\pmb0}^{\pmb{N-1}}\ketpmb{x}
$$

Now, we evaluate the oracle on both input and output registers,

$$\def\ket#1{\left|\ensuremath{#1}\right\rangle}
\def\ketpmb#1{\ket{\pmb{#1}}}
U_f\left(\frac{1}{\sqrt{N}}\sum_{\pmb{x}}\ketpmb{x}\ketpmb{0}\right) = \frac{1}{\sqrt{N}}\sum_{\pmb{x}}\ketpmb{x}\ket{f(\pmb{x})}
$$

To simplify the next steps of the calculation, we measure and discard all the qubits in the output register. Since this is a partial measurement, even though all the output qubits will collapse, the entangled input qubits may remain in superposition. We had equal superposition of all possible inputs before measurement and every output $$\left|f(\pmb{x})\right\rangle$$ is entangled with the input states $$\left|\pmb{x}\right\rangle$$ and $$\left|\pmb{x}\oplus\pmb{s}\right\rangle$$. For simplicity of the following calculation steps, we are splitting the two cases of oracle function.

#### Two-to-One Case
If the function is a two-to-one (i.e. $$\pmb{s}\neq\pmb0$$), after the measurement the output register will collapse to one specific output, say $$\left|f(\pmb{\tilde{x}})\right\rangle$$, and so the post-measurement result will be,

$$\def\ket#1{\left|\ensuremath{#1}\right\rangle}
\def\ketpmb#1{\ket{\pmb{\tilde{#1}}}}
\frac{1}{\sqrt2}\big(\ketpmb{x}+\ket{\pmb{\tilde{x}}\oplus\pmb{s}}\big)\ket{f(\pmb{\tilde{x}})}
$$

Next, we discard the output qubits and only concern ourselves with the input qubits. After applying the last Hadamard gate we get the state,

$$\def\ket#1{\left|\ensuremath{#1}\right\rangle}
\def\ketpmb#1{\ket{\pmb{\tilde{#1}}}}
H^{\otimes n}\frac{1}{\sqrt2}\big(\ketpmb{x}+\ket{\pmb{\tilde{x}}\oplus\pmb{s}}\big) = \frac{1}{\sqrt2}H^{\otimes n}\ketpmb{x} + \frac{1}{\sqrt2}H^{\otimes n}\ket{\pmb{\tilde{x}}\oplus\pmb{s}}
$$

To compute this, we note the fact that,

$$\def\ket#1{\left|\ensuremath{#1}\right\rangle}
\def\ketpmb#1{\ket{\pmb{#1}}}
H^{\otimes n}\ketpmb{k} = \frac{1}{\sqrt{N}}\sum_{\pmb{j}=\pmb0}^{\pmb{N-1}} (-1)^{\pmb{j}\odot\pmb{k}}\ketpmb{j}
$$

Substituting this expression in our earlier state we get,

$$\def\ket#1{\left|\ensuremath{#1}\right\rangle}
\def\ketpmb#1{\ket{\pmb{#1}}}
\begin{align*}
H^{\otimes n}\frac{1}{\sqrt2}\big(\ketpmb{\tilde{x}}+\ket{\pmb{\tilde{x}}\oplus\pmb{s}}\big) &= \frac{1}{\sqrt{2N}}\sum_{\pmb{j}} \left[ (-1)^{\pmb{j}\odot\pmb{\tilde{x}}} + (-1)^{\pmb{j}\odot(\pmb{\tilde{x}}\oplus\pmb{s})} \right]\ket{\pmb{j}}\\
&= \frac{1}{\sqrt{2N}}\sum_{\pmb{j}}(-1)^{\pmb{j}\odot\pmb{\tilde{x}}} \left[ 
1 + (-1)^{\pmb{j}\odot\pmb{s}} \right] \ketpmb{j}
\end{align*}
$$

Since $$\pmb{s}\neq\pmb0$$ for the case of two-to-one function, for $$\tfrac{N}{2}$$ values of $$\pmb{j}$$ we will get $$\pmb{j}\odot\pmb{s}=0$$ and for the rest we will get $$\pmb{j}\odot\pmb{s}=1$$. For the later case, the coefficient of $$|\pmb{j}\rangle$$ will vanish, because $$[1+(-1)^1]=0$$. And only the states for which $$\pmb{j}\odot\pmb{s}=0$$ will survive, because $$[1+(-1)^0]=2$$. So the state can be written as,

$$\def\ket#1{\left|\ensuremath{#1}\right\rangle}
\def\ketpmb#1{\ket{\pmb{#1}}}
H^{\otimes n}\frac{1}{\sqrt2}\big(\ketpmb{\tilde{x}}+\ket{\pmb{\tilde{x}}\oplus\pmb{s}}\big) = 
\sqrt{\frac{2}{N}}\sum_{\substack{\pmb{j}\\\pmb{j}\odot\pmb{s}=0}}(-1)^{\pmb{j}\odot\pmb{\tilde{x}}}\ketpmb{j}
$$

Next, we do a measurement on the input register. So, after measurement we get a random state $$|\pmb{j}\rangle$$ for which it is true that $$\pmb{j}\odot\pmb{s}=0}$$. Lastly, we repeat this circuit multiple times and perform classical post-processing on it.

#### One-to-One Case
If the function was one-to-one, then $$\pmb{s}=\pmb{0}$$ and $$\left|\pmb{x}\right\rangle = \left|\pmb{x}\oplus\pmb{s}\right\rangle$$. So after measurement of the output qubit, we will get a system of the form
$$\def\ket#1{\left|\ensuremath{#1}\right\rangle}
\def\ketpmb#1{\ket{\pmb{\tilde{#1}}}}
\ketpmb{x}\ket{f(\pmb{\tilde{x}})}
$$. After the final Hadamard gates a similar calculation will apply,

$$\def\ket#1{\left|\ensuremath{#1}\right\rangle}
\def\ketpmb#1{\ket{\pmb{#1}}}
H^{\otimes n}\ketpmb{\tilde{x}} = \frac{1}{\sqrt{N}}\sum_{\pmb{j}=\pmb0}^{\pmb{N-1}} (-1)^{\pmb{j}\odot\pmb{\tilde{x}}}\ketpmb{j}
$$

Since this state is an equal superposition of all possible input states (with/without a phase), if  we do a measurement, we have an equal chance of getting any of the input states. Although the calculation path for the one-to-one case was a bit different from the two-to-one case, the fact that we measure only states $$|\pmb{j}\rangle$$ where $$\pmb{j}\odot\pmb{s}=0$$ remains true (becuase $$\pmb{s}=\pmb{0}$$).

### Classical Post-processing
After executing the quantum circuit $$n$$ times, we will have a set of $$\pmb{j}$$'s for which $$\pmb{j}\odot\pmb{s}=0$$. Let us label them as $$\pmb{j_1},\pmb{j_2},\pmb{j_3},\dots,\pmb{j_n}$$. Using them we can set a system of $$n$$ linear equations to find all the unknown bits of $$\pmb{s}$$.

$$\begin{align*}
\pmb{j_1}\odot\pmb{s}&=j_{1_0}s_0\oplus j_{1_1}s_1\oplus\dots\oplus j_{1_{n-1}}s_{n-1}&=0\\
\pmb{j_2}\odot\pmb{s}&=j_{2_0}s_0\oplus j_{2_1}s_1\oplus\dots\oplus j_{2_{n-1}}s_{n-1}&=0\\
\pmb{j_3}\odot\pmb{s}&=j_{3_0}s_0\oplus j_{3_1}s_1\oplus\dots\oplus j_{3_{n-1}}s_{n-1}&=0\\
&\qquad\dots&\dots\\
\pmb{j_n}\odot\pmb{s}&=j_{n_0}s_0\oplus j_{n_1}s_1\oplus\dots\oplus j_{n_{n-1}}s_{n-1}&=0
\end{align*}$$

If these equations are linearly independent, then we can successfully find $$\pmb{s}$$. In fact, it is more likely that they are linearly dependent. But how likely is it? And can we still achieve a set of linearly independent set of equations? Actually, yes. 

Since $$\pmb{j_1},\pmb{j_2},\pmb{j_3},\dots,\pmb{j_n}$$ are just binary strings, it is relatively easy to check if they are linearly independent or not. For example, $$\{\pmb{j_1},\pmb{j_2}\}$$ can only be linearly dependent if they are equal. Then the likelihood of $$\pmb{j_2}$$ being linearly dependent from $$\pmb{j_1}$$ is then $$\tfrac{1}{2^n}$$. Next, $$\{\pmb{j_1},\pmb{j_2},\pmb{j_3}\}$$ is linearly dependent iff $$\pmb{j_3}=\pmb{j_1}$$ or $$\pmb{j_3}=\pmb{j_2}$$ or $$\pmb{j_3}=\pmb{j_1}\oplus\pmb{j_2}$$. So the probability of $$\pmb{j_3}$$ being linearly dependent on previous equations will be $$\tfrac{3}{2^n}$$.  Similarly $$\pmb{j_4}$$ will be linearly dependent if $$\pmb{j_4}=\alpha_1\pmb{j_1}+\alpha_2\pmb{j_2}+\alpha_3\pmb{j_3}$$ where $$\alpha_i=1$$ for any $$i\in[1,3]$$. Since there $$7$$ possibilities, the probability will be $$\tfrac{7}{2^n}$$.

In general, the $$k$$-th equation will be linearly dependent from all previous equations, iff $$\pmb{j_k}=\sum_{i=1}^{k-1}\alpha_i\pmb{j_i}$$ where $$\alpha_i=1$$ for any $$i\in[1,k)$$. The likelihood of being so is then,

$$\Pr(\text{$k$-th equation is linearly dependent}) = \frac{2^{k-1}-1}{2^n}$$ 

Then the probability that all $$n$$ equations are linearly independent is,

$$\Pr(\text{all $n$ equations are linearly independent}) = \prod_{k=1}^{n}\left(1-\frac{2^{k-1}-1}{2^n}\right)$$

Even though it is difficult to get an exact formula for the probability, we can easily find a lower bound,

$$\begin{align*}
\prod_{k=1}^{n}\left(1-\frac{2^{k-1}-1}{2^n}\right) 
&= \prod_{k=1}^{n}\left(1-\frac{2^{k-1}}{2^n}+\frac{1}{2^n}\right)\\
&\geq \prod_{k=1}^{n}\left(1-\frac{2^{k-1}}{2^n}\right)\\
&= \prod_{k=1}^{n}\left(1-\frac{1}{2^{n-k+1}}\right)\\
&= \prod_{l=1}^{n}\left(1-\frac{1}{2^{l}}\right)\\
&\geq \prod_{l=1}^{\infty}\left(1-2^{-l}\right)\\
&\approx 0.288788... \geq 0.25
\end{align*}$$

Although it is quite difficult to evaluate the infinite product, it is much easier to prove using induction that the value is at least 0.25. In fact to prove this, let us prove a stronger statement: $$\prod_{l=1}^{n}\left(1-2^{-l}\right)\geq0.25+2^{-(n+1)}$$. The base case $$n=1$$ is trivially true. For the inductive step, we have to show that,

$$\begin{align*}
%\prod_{l=1}^{n+1}\left(1-2^{-l}\right) &\geq 0.25 + 2^{-(n+2)}\\
%or, \left(1-2^{-(n+1)}\right)\prod_{l=1}^{n}\left(1-2^{-l}\right)
%&\geq 0.25 + 2^{-(n+2)}\\
&\left(0.25+2^{-(n+1)}\right)\left(1-2^{-(n+1)}\right)
\geq 0.25 + 2^{-(n+2)}\\
\implies& 0.25+2^{-(n+1)}-0.25\cdot2^{-(n+1)}-2^{-2(n+1)}
\geq 0.25 + 2^{-(n+2)}\\
\implies& 0.25\cdot2^{-(n+1)} \geq 2^{-2(n+1)}\\
\implies& 2^{(n+1)}\geq 4\\
\end{align*}$$

Since $$n\geq1$$, the inequality always holds true, proving the inductive hypothesis. When $$n\to\infty$$, in the limit, the product remains always greater than 0.25.

Now that we have shown that the probability of all $$n$$ equations being linearly independent is at least 25% for any $$n$$, we can repeat this scheme a constant amount of time to get a higher probability. For example, if we run the scheme 10 times, the probability at least get one set of linearly independent equations is, $$1-(1-0.25)^10\approx94.37\%$$. And if necessary, we can achieve even higher accuracy without increasing the query complexity $$\mathcal{O}(n)$$.
## Conclusion
Even though we require post-processing, we only queried $$\mathcal{O}(n)$$ times in the quantum subroutine. So this is our query complexity. However, it should be noted that this is not the time complexity since solving the linear equations will add more complexity. Compared to this, in the classical approach, we required a query complexity of $$\Omega(\sqrt{2^n})$$. Thus we get an exponential speedup over the classical approach. It should be noted that the inherent symmetry in the Simon's problem is what allowed us to gain exponential quantum speedup. A similar but without any hidden structure is the collision problem. Even though there is a quantum algorithm (Grover's) that gives us a speedup, it is only polynomial. The Deutsch-Jozsa problem is sometimes said to show exponential quantum speedup. However, this exponential speedup is only over deterministic algorithms. There exists randomized classical algorithm that solves Deutsch-Jozsa problem in $$\mathcal{O}(1)$$ queries. Thus Simon's algorithm demonstrates without a doubt that there exists quantum algorithms that can solve some problems in polynomial queries whereas classical algorithms require exponential queries.
## Notation
* $$\mathbb{B}$$: the set of binary digits i.e. $$\{0,1\}$$.
* $$\mathbb{B}^n$$: the set of all possible $$n$$-bit binary strings.
* $$\pmb{x}$$: always bold; an element of $$\mathbb{B}^k$$ and so represents a binary string.
* $$\oplus$$: bitwise xor (mod 2 addition) operation that outputs another binary string.
* $$\odot$$: binary dot product; if $$\pmb{x},\pmb{y}\in\mathbb{B}^k$$ then $$\pmb{x}\odot\pmb{y} = x_{k-1}y_{k-1} \oplus \dots \oplus x_1y_1 \oplus x_0y_0$$.
<!--* $$\pmb{7}$$: numbers but bold; represent binary vector $$(1,1,1)$$ expressed as a decimal number.-->

[1]: https://youtu.be/6qD9XElTpCE?si=7gxFxYWtNAZ_qbh4&t=660
[2]: https://aws.amazon.com/blogs/quantum-computing/simons-algorithm/
[3]: https://www.desmos.com/3d/bohm0onfec
