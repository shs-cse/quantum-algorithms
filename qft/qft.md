# Quantum Fourier Transform (Via Butterfly Diagram)
*[DFT]: Discrete Fourier Transform
*[FFT]: Fast Fourier Transform
*[QFT]: Quantum Fourier Transform

## Discrete Fourier Transformation
Let us define DFT as follows:

$$
\begin{equation}
X(k)
%=\textrm{DFT}_N[x](k)
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

## Fast Fourier Transformation
Directly computing DFT by multiplying the input vector with the DFT matrix has a time complexity of $O(N^2)$. However, there is a more efficient algorithm commonly referred to as FFT. It is a divide and conquer algorithm and has a time complexity of $O(N\log N)$. Let us discuss how FFT improves over DFT before we move onto QFT. 
Here we are going to explain radix-2 decimation-in-time[^1] FFT algorithm in details.

Let us define, $x_E$ to be the vector that is composed of the even half of the input vector $x$ and $x_O$ to be the vector that is composed of the odd half of the input vector. i.e.
$$
\begin{align*}
x_E(j) &= x(2j)&
\forall j\in
%\left[0,\tfrac{N}{2}\right)
\Z_\frac{N}{2}
\\
x_O(j) &= x(2j+1)&
\forall j\in
%\left[0,\tfrac{N}{2}\right)
\Z_\frac{N}{2}
\end{align*}
$$

Both $x_E$ and $x_O$ has a length of $\frac{N}{2}$. And since $x$ has a period of $N$, both $x_E$ and $x_O$ has a period of $\frac N2$. Let $X_E$ and $X_O$ to be the DFT applied output vector of $x_E$ and $x_O$ respectively. 

$$
\begin{align*}
X_E(k) &= \frac{1}{\sqrt{\frac{N}{2}}}\sum_{j=0}^{\frac{N}{2}-1}x_E(j)\omega_\frac{N}{2}^{jk}\\
X_O(k) &= \frac{1}{\sqrt{\frac{N}{2}}}\sum_{j=0}^{\frac{N}{2}-1}x_O(j)\omega_\frac{N}{2}^{jk}\\
\end{align*}
$$

Also notice that, $\omega_N^2 = \left(e^{i\frac{2\pi}{N}}\right)^2=e^{i\frac{2\pi}{\frac{N}{2}}} = \omega_\frac{N}{2}$ and $\omega_N^{\frac N2} = e^{i\frac{2\pi}{N}\cdot\frac N2} = e^{i\pi}=-1$.

Now, we can rewrite equation $(1)$ as follows:
$$
\begin{align*}
X(k) &= \frac{1}{\sqrt{N}}\sum_{j=0}^{N-1}x(j)\omega_N^{jk}\\
&= \frac{1}{\sqrt{N}}\sum_{j\in\textrm{even}}x(j)\omega_N^{jk} +
\frac{1}{\sqrt{N}}\sum_{j\in\textrm{odd}}x(j)\omega_N^{jk}\\
%
&= \frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x(2j)\omega_N^{2jk} +
\frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x(2j+1)\omega_N^{(2j+1)k}\\
%
&= \frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x_E(j)\omega_N^{2jk} +
\frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x_O(j)\omega_N^{2jk}\omega_N^{k}
&[\because x_E,x_O]\\
%
&= \frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x_E(j)\omega_{\frac N2}^{jk} +
\frac{\omega_N^{k}}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x_O(j)\omega_{\frac N2}^{jk}
&\left[\because \omega_N^2=\omega_\frac{N}{2}\right]\\
%
&= \frac{1}{\sqrt{2\cdot\frac N2}}\sum_{j=0}^{\frac{N}{2}-1}x_E(j)\omega_{\frac N2}^{jk} +
\frac{\omega_N^{k}}{\sqrt{2\cdot\frac N2}}\sum_{j=0}^{\frac{N}{2}-1}x_O(j)\omega_{\frac N2}^{jk}\\
%
&= \frac{1}{\sqrt2}\frac{1}{\sqrt{\frac N2}}\sum_{j=0}^{\frac{N}{2}-1}x_E(j)\omega_{\frac N2}^{jk} +
\frac{\omega_N^{k}}{\sqrt2}\frac{1}{\sqrt{\frac N2}}\sum_{j=0}^{\frac{N}{2}-1}x_O(j)\omega_{\frac N2}^{jk}\\
%
&= \frac{1}{\sqrt2}X_E(k) +
\frac{\omega_N^{k}}{\sqrt2}X_O(k)\\
%
\end{align*}
$$

Thus $N$-point DFT can be expressed as a linear combination of two $\frac N2$-point DFT. There is actually one more optimization we can apply here. Recall that, $x_E$ and $x_O$ have periods of $\frac N2$. As such, $X_E$ and $X_O$ also have periods of $\frac N2$.

$$
\begin{align*}
X_E\left(k+\frac N2\right) &= X_E(k)\\
X_O\left(k+\frac N2\right) &= X_O(k)
\end{align*}
$$

Using these identities,
$$
\begin{align*}
X\left(k+\frac N2\right) 
&= \frac{1}{\sqrt2}X_E\left(k+\frac N2\right) +
\frac{\omega_N^{k+\frac N2}}{\sqrt2}X_O\left(k+\frac N2\right)\\
&= \frac{1}{\sqrt2}X_E(k) +
\frac{\omega_N^k\omega_N^\frac N2}{\sqrt2}X_O(k)
&\left[\because\text{period of }\tfrac N2\right]\\
&= \frac{1}{\sqrt2}X_E(k) -
\frac{\omega_N^k}{\sqrt2}X_O(k)
&\left[\because\omega_N^{\frac N2}=-1\right]\\
\end{align*}
$$

Thus, to compute $X$ we only have to iterate over $k$ from $0$ to $\left(\frac N2-1\right)$.

<div align="center">
<img src="https://shs-cse/github.io/quantum-algorithms/blob/main/qft/fft_recursion.svg">
</div>







<!--
Here we are going to explain radix-2 decimation-in-frequency[^1] FFT algorithm in details.

Let us define, $x_L$ to be the vector that is composed of the left half of the input vector $x$ and $x_R$ to be the vector that is composed of the right half of the input vector. i.e.
$$
\begin{align*}
x_L(j) &= x(j)&
\forall j\in
%\left[0,\tfrac{N}{2}\right)
\Z_\frac{N}{2}
\\
x_R(j) &= x\left(j+\frac{N}{2}\right)&
\forall j\in
%\left[0,\tfrac{N}{2}\right)
\Z_\frac{N}{2}
\end{align*}
$$

Both $x_L$ and $x_R$ has a length of $\frac{N}{2}$. Let $X_L$ and $X_R$ be the DFT applied output vector of $x_L$ and $x_R$ correspondingly. 

$$
\begin{align*}
X_L(k) &= \frac{1}{\sqrt{\frac{N}{2}}}\sum_{j=0}^{\frac{N}{2}-1}x_L(j)\omega_\frac{N}{2}^{jk}\\
X_R(k) &= \frac{1}{\sqrt{\frac{N}{2}}}\sum_{j=0}^{\frac{N}{2}-1}x_R(j)\omega_\frac{N}{2}^{jk}\\
\end{align*}
$$

Notice that, $\omega_N^2 = \left(e^{i\frac{2\pi}{N}}\right)^2=e^{i\frac{2\pi}{\frac{N}{2}}} = \omega_\frac{N}{2}$ and $\omega_N^\frac{N}{2} = -1$.

Now, we can rewrite equation $(1)$ as follows:
$$
\begin{align*}
X(k) &= \frac{1}{\sqrt{N}}\sum_{j=0}^{N-1}x(j)\omega_N^{jk}\\
&= \frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x(j)\omega_N^{jk} +
\frac{1}{\sqrt{N}}\sum_{j=\frac{N}{2}}^{N-1}x(j)\omega_N^{jk}\\
&= \frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x(j)\omega_N^{jk} +
\frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x\left(j+\frac{N}{2}\right)\omega_N^{\left(j+\frac{N}{2}\right)k}\\
&= \frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x_L(j)\omega_N^{jk} +
\omega_N^{\frac{N}{2}k}\frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x_R(j)\omega_N^{jk}
&\left[\because j\leftarrow j+\tfrac{N}{2}\right]\\
&= \frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x_L(j)\omega_N^{jk} +
(-1)^k\frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x_R(j)\omega_N^{jk}
&\left[\because\omega_N^\frac{N}{2} = -1\right]
\end{align*}
$$

We can split this into even and odd parts. For the even parts, we get,
$$
\begin{align*}
X(2k) &= \frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x_L(j)\omega_N^{2jk} +
(-1)^{2k}\frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x_R(j)\omega_N^{2jk}\\
&= \frac{1}{\sqrt{2\cdot\frac{N}{2}}}\sum_{j=0}^{\frac{N}{2}-1}x_L(j)\omega_{\frac{N}{2}}^{jk} +
\frac{1}{\sqrt{2\cdot\frac{N}{2}}}\sum_{j=0}^{\frac{N}{2}-1}x_R(j)\omega_{\frac N2}^{jk}
&\left[\because\omega_N^2=\omega_\frac{N}{2}\right]\\
&=\frac{1}{\sqrt2}X_L(k)+\frac{1}{\sqrt2}X_R(k)
\end{align*}
$$

Similar calculation for the odd parts yields,
$$
\begin{align*}
X(2k+1) &= \frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x_L(j)\omega_N^{j(2k+1)} +
(-1)^{2k+1}\frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x_R(j)\omega_N^{j(2k+1)}\\
 &= \frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x_L(j)\omega_N^{j(2k+1)} -\frac{1}{\sqrt{N}}\sum_{j=0}^{\frac{N}{2}-1}x_R(j)\omega_N^{j(2k+1)}\\
%
%&= \frac{1}{\sqrt{2\cdot\frac{N}{2}}}\sum_{j=0}^{\frac{N}{2}-1}x_L(j)\omega_{\frac{N}{2}}^{jk} +
%\frac{1}{\sqrt{2\cdot\frac{N}{2}}}\sum_{j=0}^{\frac{N}{2}-1}x_R(j)\omega_{\frac N2}^{jk}
%&\left[\because\omega_N^2=\omega_\frac{N}{2}\right]\\
%&=\frac{1}{\sqrt2}X_L(k)+\frac{1}{\sqrt2}X_R(k)
\end{align*}
$$
-->




<!--## Resources-->
[^0]: https://katex.org/docs/supported
[^0]: https://github.com/KaTeX/KaTeX/issues/2003#issuecomment-1911672998
[^1]: https://www.cmlab.csie.ntu.edu.tw/cml/dsp/training/coding/transform/fft.html

