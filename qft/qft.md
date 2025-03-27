# Quantum Fourier Transform (Via Butterfly Diagram)
*[DFT]: Discrete Fourier Transform
*[FFT]: Fast Fourier Transform
*[QFT]: Quantum Fourier Transform
## Discrete Fourier Transformation
Let us define DFT as follows:

$$
\begin{equation}
F(k)=\frac{1}{\sqrt{N}}\sum_{j=0}^{N-1}x(j)\omega_N^{jk}
\end{equation}
$$

where $N=2^n,~\omega_N = e^{i\frac{2\pi}{N}},~x(j)$ is the $n$-th element of the input vector $x$ and $F(k)$ is the $k$-th element of the transformed vector.

The goal of QFT is to build a circuit that transforms an amplitude encoded vector. If $\ket{x}$ is a $n$ qubit state, then equation $(1)$ can be written as follows: 

$$
\braket{k|\textrm{QFT}|x} 
= \frac{1}{\sqrt{N}}
\sum_{j=0}^{N-1}\braket{j|x}\omega_N^{jk}
$$

where $k\in\mathbb{Z}_N$. Taking, $x=l\in\mathbb{Z}_N$ we can build up the $\textrm{QFT}$ matrix since $\braket{k|\textrm{QFT}|l}$ is the element on the $k$-th row and $l$-th column of the $\textrm{QFT}$ matrix.

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
Blindly multiplying the DFT matrix with the input vector has the time complexity of $O(N^2)$. However, there is a more efficient algorithm commonly referred to as FFT. It is a divide and conquer algorithm and has a time complexity of $O(N\log N)$. Let us discuss FFT before we move onto QFT.


