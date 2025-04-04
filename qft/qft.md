# Quantum Fourier Transform Via Butterfly Diagram (incomplete...)
> *April 1, 2025*
> **Author**
> &nbsp;&nbsp;&nbsp;&nbsp;Shadman Shahriar (SDS)
> &nbsp;&nbsp;&nbsp;&nbsp;Lecturer, Brac University, Dhaka

*[DFT]: Discrete Fourier Transform
*[FFT]: Fast Fourier Transform
*[QFT]: Quantum Fourier Transform
*[DIT-FFT]: Decimation in Time Fast Fourier Transform
*[DIF-FFT]: Decimation in Frequency Fast Fourier Transform

## Discrete Fourier Transform
Let us define $N$-point DFT as follows:

$$
\begin{equation}
X(k) 
=\textrm{DFT}_N[x](k)
=\frac{1}{\sqrt{N}}\sum_{j=0}^{N-1}x(j)\omega_N^{jk}
\end{equation}
$$

where $N=2^n,~\omega_N = e^{i\frac{2\pi}{N}},~x(j)$ is the $n$-th element of the input vector $x$ and $X(k)$ is the $k$-th element of the transformed vector. In DFT, it is implicitly assumed that the input vector cycles back after $N$ data points. i.e.
$$x(j+N)=x(j)$$

The goal of QFT is to build a circuit that transforms an amplitude encoded vector. If $\ket{x}$ is a $n$ qubit state, then equation $(1)$ can be written as follows: 

$$
\braket{k|\textrm{QFT}|x} 
= \frac{1}{\sqrt{N}}
\sum_{j=0}^{N-1}\braket{j|x}\omega_N^{jk}
$$

where $k\in\Z_N$. Taking, $x=l\in\mathbb{Z}_N$ we can build up the $\textrm{QFT}$ matrix since $\braket{k|\textrm{QFT}|l}$ is the element on the $k$-th row and $l$-th column of the $\textrm{QFT}$ matrix.

$$\def\bra#1{\left\langle#1\right|}\def\ket#1{\left|#1\right\rangle}
\def\braket#1#2{\left\langle#1\middle|#2\right\rangle}
\bra{k}\textrm{QFT}\ket{l}
= \frac{1}{\sqrt{N}}\sum_{j=0}^{N-1}\braket{j}{l}\omega_N^{jk}
= \frac{1}{\sqrt{N}}\sum_{j=0}^{N-1}\delta_{jl}\omega_N^{jk}
= \frac{\omega_N^{lk}}{\sqrt{N}}$$

If we write it as a matrix in the computational basis, we get,

$$\textrm{QFT} = \frac{1}{\sqrt N}\begin{pmatrix}
1&1&1&\dots&1\\
1&\omega_N&\omega_N^2&\dots&\omega_N^{N-1}\\
1&\omega_N^2&\omega_N^4&\dots&\omega_N^{2(N-1)}\\
\vdots&\vdots&\vdots&\ddots&\vdots\\
1&\omega_N^{N-1}&\omega_N^{2(N-1)}&\dots&\omega_N^{(N-1)^2}
\end{pmatrix}$$

This is of course, same as the DFT matrix (maybe except for the normalization factor). One of the important property of this matrix is that it is an unitary matrix and thus it is possible to build a quantum circuit for it.

## Fast Fourier Transform
Directly computing DFT by multiplying the input vector with the DFT matrix has a time complexity of $O(N^2)$. However, there is a more efficient algorithm commonly referred to as FFT. It is a divide and conquer algorithm and has a time complexity of $O(N\log N)$. Let us discuss how FFT improves over DFT before we move onto QFT. 

Here we are going to explain radix-2 decimation-in-frequency DIF-FFT algorithm[^2] in details. Simply because this one (rather than DIT-FFT) closely matches the traditionally used QFT circuit.

Let us define, vectors $x_A$ and $x_B$ of length $\frac N2$ as follows:
$$
\begin{align*}
x_A(j) &= \frac{1}{\sqrt2}\left[x(j)+x\!\left(j+\frac{N}{2}\right)\right]&
\forall j\in
%\left[0,\tfrac{N}{2}\right)
\Z_\frac{N}{2}
\\
x_B(j) &= \frac{\omega_N^j}{\sqrt2}\left[x(j)-x\!\left(j+\frac{N}{2}\right)\right]&
\forall j\in
%\left[0,\tfrac{N}{2}\right)
\Z_\frac{N}{2}
\end{align*}
$$

Both $x_A$ and $x_B$ has a length of $\frac{N}{2}$. Let $X_A$ and $X_B$ be the output vector after applying the $\frac N2$-point DFT on input vectors $x_A$ and $x_B$ respectively. 

$$
\begin{align*}
X_A(k) = \textrm{DFT}_{\frac N2}[x_A](k)
&= \frac{1}{\sqrt{\frac{N}{2}}}\sum_{j=0}^{\frac{N}{2}-1}x_A(j)\omega_\frac{N}{2}^{jk}\\
X_B(k) = \textrm{DFT}_{\frac N2}[x_B](k)
&= \frac{1}{\sqrt{\frac{N}{2}}}\sum_{j=0}^{\frac{N}{2}-1}x_B(j)\omega_\frac{N}{2}^{jk}\\
\end{align*}
$$

Also notice that, $\omega_N^2 = \left(e^{i\frac{2\pi}{N}}\right)^2=e^{i\frac{2\pi}{\frac{N}{2}}} = \omega_\frac{N}{2}$ and $\omega_N^{\frac N2} = e^{i\frac{2\pi}{N}\cdot\frac N2} = e^{i\pi}=-1$.

Now, we can rewrite equation $(1)$ as follows:
$$
\begin{align*}
X(k) &= \frac{1}{\sqrt{N}}\sum_{j=0}^{N-1}x(j)\omega_N^{jk}\\
&= \frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x(j)\omega_N^{jk} +
\frac{1}{\sqrt{N}}\sum_{j=\frac{N}{2}}^{N-1}x(j)\omega_N^{jk}\\
&= \frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x(j)\omega_N^{jk} +
\frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x\!\left(j+\frac{N}{2}\right)\omega_N^{\left(j+\frac{N}{2}\right)k}\\
&= \frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}
\left[x(j) + x\!\left(j+\frac{N}{2}\right)\omega_N^{\frac{N}{2}k}\right]\omega_N^{jk}\\
&= \frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}
\left[x(j) + x\!\left(j+\frac{N}{2}\right)(-1)^k\right]\omega_N^{jk}
&\left[\because\omega_N^\frac{N}{2} = -1\right]
\end{align*}
$$

We can split the $X$ vector into even and odd entries. For the even entries $(k=2m)$ we get,
$$
\begin{align*}
X(2m) &= \frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}
\left[x(j) +(-1)^{2m} x\!\left(j+\frac{N}{2}\right)\right]\omega_N^{2jm}\\
&= \frac{1}{\sqrt{\frac N2\cdot2}}\sum_{j=0}^{\frac{N}{2}-1}
\left[x(j) +x\!\left(j+\frac{N}{2}\right)\right]\omega_{\frac N2}^{jm}
&\left[\because\omega_N^2=\omega_\frac{N}{2}\right]\\
&= \frac{1}{\sqrt{\frac N2}}\sum_{j=0}^{\frac{N}{2}-1}
\frac{1}{\sqrt2}\left[x(j) +x\!\left(j+\frac{N}{2}\right)\right]\omega_{\frac N2}^{jm}
&\left[\because\omega_N^2=\omega_\frac{N}{2}\right]\\
&= \frac{1}{\sqrt{\frac N2}}\sum_{j=0}^{\frac{N}{2}-1}
x_A(j)\omega_{\frac N2}^{jm}
&[\because\text{definition of }x_A(j)]\\
&=X_A(m)
\end{align*}
$$

Similar calculation for the odd entries $(k=2m+1)$ yields,
$$
\begin{align*}
X(2m+1) &= \frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}
\left[x(j) +(-1)^{2m+1} x\!\left(j+\frac{N}{2}\right)\right]\omega_N^{j(2m+1)}\\
&= \frac{1}{\sqrt{\frac N2\cdot2}}\sum_{j=0}^{\frac{N}{2}-1}
\left[x(j) -x\!\left(j+\frac{N}{2}\right)\right]\omega_N^j\omega_N^{2jm}\\
&= \frac{1}{\sqrt{\frac N2}}\sum_{j=0}^{\frac{N}{2}-1}
\frac{\omega_N^j}{\sqrt2}\left[x(j) -x\!\left(j+\frac{N}{2}\right)\right]\omega_N^{2jm}\\
&= \frac{1}{\sqrt{\frac N2}}\sum_{j=0}^{\frac{N}{2}-1}
x_B(j)\omega_{\frac N2}^{jm}
&\left[\because\omega_N^2=\omega_\frac{N}{2}\text{, and definition of }x_B(j)]\right]\\
&=X_B(m)
\end{align*}
$$

Both results can be summarized as follows:
$$
% \textrm{DFT}_N[x](k) 
X(k) = \begin{cases}
X_A(m)&\text{if }k=2m\\
X_B(m)&\text{if }k=2m+1\\%
\end{cases}
$$

We may write a python program for the FFT algorithm as follows:
```py
import numpy as np

def FFT(x):
  "Decimation in Frequency Fast Fourier Transform"
  N = len(x) # N = 2**n
  if N==1:
    return x
  w = np.exp(2j*np.pi/N)
  x_A = [(x[j] + x[j+N//2])/np.sqrt(2) for j in range(N//2)]
  x_B = [(x[j] - x[j+N//2])*w**j/np.sqrt(2) for j in range(N//2)]
  X = np.empty(N, dtype=complex)
  X[::2] = FFT(x_A)
  X[1::2] = FFT(x_B)
  return X
```

## Butterfly Diagram
The DIF-FFT algorithm presented above can be neatly represented with a butterfly diagram. Here is a single recursive unit of the algorithm:

<div align="center">
<img src="https://shs-cse.github.io/quantum-algorithms/qft/dif_fft_butterfly_unit.svg" width="500ch">
</div>

To compute $x_A$ and $x_B$ from $x$, we have to iterate over $j$ from $0$ to $\left(\frac N2-1\right)$. Thus we obtain two vectors with length $\frac N2$. Then, we keep reiterating in a recursive manner to find $X$. For example, after two stages, we will have computed $x_{AA}, x_{AB}, x_{BA}, x_{BB}$ and each of them will have $\frac N4$ elements.  We continue this process until we reach single-element vectors, and finally it is trivially its own DIF-FFT.

If we unravel the recursion, we get a complete diagram to compute $X$ from $x$. Here is the complete diagram for $N=2^n=2^3=8$.

<div align="center">
<img src="https://shs-cse.github.io/quantum-algorithms/qft/dif_fft_8_butterfly_full.svg" width="100%">
</div>

The diagonals all have a factor of $\frac{1}{\sqrt2}$. Here we observe that, $X(6)\equiv X_{ABB}$ *(notice the light grey text above nodes after each recursive unit)*. But since $x_{ABB}$ is just a single-element vector, $X_{ABB} = x_{ABB}$. There is a nice and easy pattern to figure out that $X(6)$ is same as $x_{ABB}$ *(and so on)*. Notice that if you replace $A$ with $0$ and $B$ with $1$, the subscript becomes $011$, which is $3$ in binary. And you will notice that it's situated on the $x(3)$ row. Also, if we reverse the bits, we get $110$, which is $6$ in binary. And the output on that row is $X(6)$. In other words, we have to reverse the bits again to get the actual $X$ vector.


## Quantum Fourier Transform
### The Hadamard Part
In QFT, we use an $n$-qubit system where $N=2^n$ to denote the input vector $\ket{x}$. The values are encoded in the amplitudes of the computational basis states. In other words, up until now the $j$-th element of $x$ was denoted as $x(j)$. But in QFT it will be represented with $\braket{j|x}$, and also, $\ket{X}=\textrm{QFT}\ket{x}$. To build the quantum circuit for QFT, our strategy will be to just convert the butterfly diagram for the $N$-bit classical operations into $n$-qubit quantum gates.

Notice that the recursive butterfly unit has similarities to quantum gates. For example, we may consider the action of Hadamard gate on a single qubit. Let $\ket\psi=\alpha\ket0+\beta\ket1$ be an arbitrary qubit state. Then $\alpha=\braket{0|\psi}$ and $\beta=\braket{1|\psi}$. Now, if we apply the Hadamard gate, we get,
$$
\textrm{H}\!\ket\psi=\alpha\ket++\beta\ket-=\frac{\alpha+\beta}{\sqrt2}\ket0+\frac{\alpha-\beta}{\sqrt2}\ket1
$$

Let us draw the butterfly diagram of the action of the Hadamard gate in computational basis:

<div align="center">
<img src="https://shs-cse.github.io/quantum-algorithms/qft/hadamard_butterfly.svg" width="500ch">
</div>

Now if we apply the same technique on a larger quantum system, we get the almost exact recursive butterfly unit as before, except for the $\omega_N^j$ phase factor. For example, for $N=2^3=8$ case, in the first stage, we see Hadamard like butterfly diagrams connecting as follows:

| Input to Stage 1 | Row Index in Binary  | Output of Stage 1<br>*(ignoring $\omega_N^j$ factor)*|
|:-:|:-:|:-:|
| $x(0)\\x(4)$ | $\red{0}00\\\red{1}00$ | $x_{\red{A}}(0)\\x_{\red{B}}(0)$ |
| $x(1)\\x(5)$ | $\red{0}01\\\red{1}01$ | $x_{\red{A}}(1)\\x_{\red{B}}(1)$ |
| $x(2)\\x(6)$ | $\red{0}10\\\red{1}10$ | $x_{\red{A}}(2)\\x_{\red{B}}(2)$ |
| $x(3)\\x(7)$ | $\red{0}11\\\red{1}11$ | $x_{\red{A}}(3)\\x_{\red{B}}(3)$ |


We can clearly see that only the first qubit changes and the other remains as it is. So, if we apply Hadamard on the MSB, we will get the desired butterfly diagram for the first stage. 

Similar argument applies to other stages as well. For example, in the second stage, the butterfly diagrams connects as follows:


| Input to Stage 2 | Row Index in Binary  | Output of Stage 2<br>*(ignoring $\omega_N^j$ factor)*|
|:-:|:-:|:-:|
| $x_A(0)\\x_A(2)$ | $0\red{0}0\\0\red{1}0$ | $x_{A\red{A}}(0)\\x_{A\red{B}}(0)$ |
| $x_A(1)\\x_A(3)$ | $0\red{0}1\\0\red{1}1$ | $x_{A\red{A}}(1)\\x_{A\red{B}}(1)$ |
| $x_B(0)\\x_B(2)$ | $1\red{0}0\\1\red{1}0$ | $x_{B\red{A}}(0)\\x_{B\red{B}}(0)$ |
| $x_B(1)\\x_B(3)$ | $1\red{0}1\\1\red{1}1$ | $x_{B\red{A}}(1)\\x_{B\red{B}}(1)$ |

Let us prove that this trend holds in general.  Say, we apply Hadamard gate on the $b$-th qubit of the $n$-qubit quantum system $\ket x$. Then, applying $\textrm{H}$ on the basis vector $\ket j$ will only contribute to $\Ket{j\oplus2^b}$ and $\ket j$ itself. For example, in the first stage, we have $b=n-1$, resulting in $\Ket{j\oplus2^b}=\Ket{j\oplus2^{n-1}}=\Ket{j\oplus\frac N2}$. Since we limit $j\in\Z_\frac{N}{2}$, we have $\Ket{j\oplus\frac N2}=\Ket{j+\frac N2}$, which yields the correct recursive unit for stage 1. Similarly, for stage 2, taking $b=n-2$ yields the correct answer since in stage 2 we perform $\frac N2$-point DFT and similar argument holds separately for $\ket{x_A}$ and $\ket{x_B}$.

### The Phase Factor Part
Now that we have figured out the Hadamard part already, only accounting for the phase factor part remains. This can be easily done with the help of phase gates. For example, in the $N=2^3=8$ case, as part of stage 2, we apply $\omega_\frac{N}{2}^1=\omega_N^2$ only on the states $\ket{011}\equiv\ket3$ and $\ket{111}\equiv\ket7$. This can be achieved by applying a $\textrm{CP}(\theta)$ gate on the two rightmost qubits. Note that I am defining the controlled phase gate as follows:
$$
\textrm{CP}(\theta) = \ket{00}\!\!\bra{00} + \ket{01}\!\!\bra{01} + \ket{10}\!\!\bra{10} + e^{i\theta}\ket{11}\!\!\bra{11}
$$

Similarly, to introduce the phase factors at the end of stage 1, we can  use only 2 different $\textrm{CP}(\theta)$ gates. One of the gates will introduce $\omega_N^1$ to both $\ket{101}\equiv\ket5$ and  $\ket{111}\equiv\ket7$. We then again apply $\omega_N^2$ to both $\ket{110}\equiv\ket6$ and  $\ket{111}\equiv\ket7$. That way, $\ket7$ will pickup $\omega_N^3$ by conjunction.

To prove that this will work in the general case, we notice that in the recursive butterfly unit, we have $\omega_N^j$ applied on the basis vectors $\Ket{j+\frac N2}$, where $j\in\Z_\frac{N}{2}$. That means, $\omega_N^j$ can be built from $\omega_N^1, \omega_N^2, \omega_N^4, \omega_N^8$ etc. since $j$ can be expressed as a binary number an we apply the phase gate $\omega_N^{2^b}$ if the  $b$-th bit of $j$ is $1$. In the previous example we saw that $\omega_N^2$ is applied to $\ket6$ and $\ket7$. This is because, if we equate $\Ket{j+\frac N2}$ with either of them, we get $j=2$ and $j=3$. Both of them has $1$ at bit index $b=1$; and so, we apply $\omega_N^{2^b}=\omega_N^{2^1}=\omega_N^{2}$ to both of them.

Although we have justified that it is possible to breakdown $\omega_N^j$ into smaller phase gates; we have not proved that controlled phase gates are necessary. However, it is easy to see that we need controlled phase gates because we don't want to apply phase gates on the basis vectors $\ket j$ where $j\in\Z_\frac{N}{2}$. In order to do so, we need to set control on the MSB and then apply the phase gate on the target qubit. The distinction here between the control and target qubit is unnecessary since $\textrm{CP}(\theta)$ gate is symmetric. For example, in the case of stage 1, we can take the $n$-th qubit as the control and take $b$-th qubit as the target qubit for a phase gate that applies the phase $\omega_N^{2^b}$ if both qubits are in state $\ket1$, otherwise does nothing.

### The Quantum Circuit
Putting them all together, this is the circuit that we get for $N=2^3=8$ *(again, not how the quantum circuit is one-to-one mapped to the butterfly diagram of DIF-FFT)*,




<!-- and,
$$
\begin{align*}
\ket{x} &= \ket{x_E} + \ket{x_O}\\
\ket{x_E}
&=\sum_{j=0}^{\frac N2-1}\ket{2j}\!\!\braket{2j|x}
=\sum_{j=0}^{\frac N2-1}\ket{j}\!\!\bra{j}\otimes\ket0\!\!\braket{0|x}\\
\ket{x_O}
&=\sum_{j=0}^{\frac N2-1}\ket{j}\!\!\bra{j}\otimes\ket1\!\!\braket{1|x}
\end{align*}
$$

Rewriting the equations for QFT, we get,
$$
\braket{k|X} = \frac{1}{\sqrt2}\braket{k|X_E} + \frac{\omega_N^{k}}{\sqrt2}\braket{k|X_O}$$
$$\left\langle k+\frac N2\middle|X\right\rangle = \frac{1}{\sqrt2}\braket{k|X_E} - \frac{\omega_N^{k}}{\sqrt2}\braket{k|X_O}
$$
-->







<!--## Resources-->
[^0]: https://katex.org/docs/supported
[^0]: https://github.com/KaTeX/KaTeX/issues/2003#issuecomment-1911672998
[^1]: https://www.cmlab.csie.ntu.edu.tw/cml/dsp/training/coding/transform/fft.html
[^2]: https://uomustansiriyah.edu.iq/media/lectures/5/5_2021_12_15!05_17_30_PM.pdf

