# Properties of Some Important Operators on Hilbert Space (Finite Dimensional)
- Any operator $(M)$ can be separated into Hermitian $(A)$ and anti-Hermitian $(iB)$ parts *[(proof)](#theorem-1)*:
$$M = A+iB\text{\quad where\quad}A^\dag=A\text{~and~}B^\dag=B$$
<!-- Table of contents generated below was 
generated with https://github.com/derlin/bitdowntoc-->
- The most important types of operators are as follows:
	* [Normal Operators $\left(N^\dag N=NN^\dag\right)$](#normal-operators-leftndag-nnndagright)
	* [Unitary Operators $\left(U^\dag = U^{-1}\right)$](#unitary-operators-leftudag-u-1right)
	* [Hermitian Operators $\left(H^\dag =H\right)$](#hermitian-operators-lefthdag-hright)
   * [Puali Operators $\{X,Y,Z\}$](#puali-operators-xyz)
   * [Orthogonal Projection Operators $\left(P^2 =P=P^\dag\right)$](#orthogonal-projection-operators-leftp2-ppdagright)
- Some other types of operators you should know:
   * [Positive-Semidefinite Operators $\left(S^\dag=S\succeq0\right)$](#positive-semidefinite-operators-leftsdagssucceq0right)
   * [Projection Operators $\left(P^2 =P\right)$](#projection-operators-leftp2-pright)
   * [Pauli Group](#pauli-group)
   * [Clifford Group $\left(C\mathcal PC^\dag=C\mathcal PC^{-1}=\mathcal P'\right)$](#clifford-group-leftcmathcal-pcdagcmathcal-pc-1mathcal-pright)




## Normal Operators $\left(N^\dag N=NN^\dag\right)$

The most important property is that they have ***Spectral Decomposition***:
- Eigenvectors for *different* eigenvalues are orthogonal. *[(proof)](#theorem-5)*
- Eigenvectors form an orthonormal basis. So $N$ can be written as: *[(proof)](#theorem-6-spectral-theorem)*
$$N=\sum_k \lambda_k \ket{e_k}\!\!\bra{e_k} \text{~where~}\braket{e_k|e_l}=\delta_{kl}$$

Some other properties:
- Eigenvalues could be real or complex.
- For any state $\ket \psi$, we have: $\big\lVert{N\!\ket \psi}\big\rVert = \big\lVert{N^\dag\!\ket\psi}\big\rVert$. *[(proof)](#theorem-3)*
- $N^\dag$ is also a normal operator. And the eigenvectors are same as $N$, but with eigenvalues conjugate of $N$'s. *[(proof)](#theorem-4)*
$$N^\dag\!\ket e=\lambda^*\ket e\text{\quad where\quad}N\!\ket e = \lambda\ket e$$
- Can be separated into Hermitian and anti-Hermitian parts $(N=A+iB)$ where they commute, $AB=BA$. *[(proof)](#theorem-2)*
- $N$ is unitarily diagonalizable. The eigenvalues are the entries of the diagonal matrix and corresponding eigenvectors make the columns of the unitary matrix.

## Unitary Operators $\left(U^\dag = U^{-1}\right)$
- $U$ is also normal because $U^\dag U = I = U U^\dag$. So spectral decomposition also applies here:  *[(proof)](#theorem-6-spectral-theorem)*
$$U=\sum_k \lambda_k \ket{e_k}\!\!\bra{e_k} \text{~where~}\braket{e_k|e_l}=\delta_{kl}$$

- Eigenvalues are genereally complex with a magnitude of $1$. So, they can be written as $e^{i\theta}$: *[(proof)](#theorem-7)*
$$|\lambda_k| = 1\quad\iff\quad\lambda_k=e^{i\theta}$$

- Preserves inner product between any two states: *[(proof)](#theorem-8)*
*(and as a result, also preserves norm of any state.)*
$$\ket{\psi'} = U\!\ket\psi\text{~and~}\ket{\phi'} = U\!\ket\phi\quad\iff\quad\braket{\psi'|\phi'}=\braket{\psi|\phi}$$
- Can be written as, $U=e^{iH}$ where $H$ is Hermitian. *[(proof)](#theorem-9)*

## Hermitian Operators $\left(H^\dag =H\right)$
Also known as self-adjoint operators.
- $H$ is also normal because $H^\dag H = H^2 = H H^\dag$. So spectral decomposition also applies here: *[(proof)](#theorem-6-spectral-theorem)*
$$H=\sum_k \lambda_k \ket{e_k}\!\!\bra{e_k} \text{~where~}\braket{e_k|e_l}=\delta_{kl}$$

- Eigenvalues are real, $\lambda_k\in\mathbb R$.  *[(proof)](#theorem-10)*
- For any state $\ket\psi$ in a Hilbert space, we get, *[(proof)](#theorem-11)*
$$\braket{\psi|H|\psi} \in\mathbb R$$
- Related to a unitary $U$ by the equation, $U=e^{iH}$. *[(proof)](#theorem-9)*

## Positive-Semidefinite Operators $\left(S^\dag=S\succeq0\right)$
Most authors also call them ***Positive operators***. But some author define Positive operators to be strictly positive $\left(S\succ0\right)$. However, in most physics context, it usually refers to Positive-semidefinite operator. And if anywhere we need the strict case, it is referred to as Positive-definite operator.
- The **defining** characteristics of positive operator is that for any state $\ket\psi$, we get, $$\braket{\psi|S|\psi} \geq 0$$
- In a Hilbert space, it is can be proven that the definition implies that $S$ is also Hermitian, $S^\dag = S$.  So no need to explicitly mention that in case of Hilbert space. However, in real vector spaces, this is not the case and Hermicity must be included in the definition. 
*For example, the operator that rotates real vectors by $60\degree$ in $\mathbb R^2$, $R(60\degree)$ on a real vector space, is not a positive operator because it's not Hermitian. Even though, for any normalized state $\ket\psi$, the inner product $\braket{\psi|R(60^\degree)|\psi}=\cos60^\degree=\frac12$. So, for any state $\ket\psi$, we have $\braket{\psi|R(60^\degree)|\psi}\geq0$.*
- Eigenvalues are non-negative, $\lambda_k\geq0$. And eigenvectors form orthonormal basis. *[(proof)](#theorem-12)*
- Has a unique Positive *(and Hermitian)* square root, $S=R^2$ where, 
$$R=\sum_k \sqrt{\lambda_k} \ket{e_k}\!\!\bra{e_k} \text{~where~}\braket{e_k|e_l}=\delta_{kl}$$
- For any operator $M$, the operator $M^\dag M$ is always a positive operator. Because for any state $\ket\psi$, we get,
$$\braket{\psi|M^\dag M|\psi} = \big\lVert M\ket\psi\big\rVert^2\geq0$$


## Projection Operators $\left(P^2 =P\right)$
Some authors mean Orthogonal projection operator. But Projection operators are more general than that. For example, $\begin{pmatrix}1&1\\0&0\end{pmatrix}$ is an Oblique projection operator.
- If $P$ projects onto the subspace $X$, then for any $\ket x\in X$,
$$P\!\ket x = \ket x$$
And for any state $\ket y\notin X$, we have $P\!\ket y=0$.
- Eigenvalues of $P$ are either $1$ or $0$:
*(But eigenvectors are not necessarily orthogonal. So, may not be a positive operator.)*
$$\lambda_k\in\{0,1\}$$

## Orthogonal Projection Operators $\left(P^2 =P=P^\dag\right)$
- By definition, $P$ is Hermitian (and by extension, also normal).
- $P$ is also positive because the eigenvalues are either $1$ or $0$:
$$\lambda_k\in\{0,1\}$$
- Since $P$ is a normal operator, spectral decomposition can be applied: *[(proof)](#theorem-6-spectral-theorem)*
$$P = \sum_k\ket{e_k}\!\!\bra{e_k} 
\text{~where~}\braket{e_k|e_l}=\delta_{kl}$$
- If $P\neq I$, then $P^{-1}$ does not exist.

## Puali Operators $\{X,Y,Z\}$
There are only 3 Pauli operators, and these are *THE* most important single qubit operators. Pauli operators have the following properties:
- Definitions in standard basis:
$$X=\begin{pmatrix}0&1\\1&0\end{pmatrix},\qquad
Y=\begin{pmatrix}0&-i\\i&0\end{pmatrix},\qquad
Z=\begin{pmatrix}1&0\\0&1\end{pmatrix}$$
- Unitary: 
$$X^\dag=X^{-1},\qquad Y^\dag=Y^{-1},\qquad Z^\dag=Z^{-1}$$
- Hermitian: 
$$X^\dag=X,\qquad Y^\dag=Y,\qquad Z^\dag=Z$$
- Involutory (inverse of itself, and so, squares to identity) since both unitary and Hermitian:
$$X^2=Y^2=Z^2=I$$
- The two eigenvalues are $+1$ and $-1$:
$$\def\eig{\operatorname{eig}}\eig(X)=\eig(Y)=\eig(Z)=\pm1$$
- Traceless (since sum of eigenvalues is $0$): 
$$\def\tr{\operatorname{tr}}\tr(X)=\tr(Y)=\tr(Z)=0$$
- Determinant is $-1$ (since product of eigenvalues is $-1$):
$$\det(X)=\det(Y)=\det(Z)=-1$$
- Anti-commutes with other Pauli operators:
$$XY=-YX,\qquad YZ=-ZY,\qquad ZX=-XZ$$
- Relation between Pauli operators:
$$XY=iZ,\qquad YZ=iX,\qquad ZX=iY$$

## Pauli Group
By including $I$ with Pauli operators and extending to $n$-qubits, we get Pauli group. In other words, $\mathcal P$ is an operator in Pauli group iff:
$$\mathcal P = i^kP_1\otimes P_2\otimes \dots\otimes P_n\quad\text{where}\quad k\in\Z_4,~P_i\in\{I,X,Y,Z\}$$

## Clifford Group $\left(C\mathcal PC^\dag=C\mathcal PC^{-1}=\mathcal P'\right)$
Operators in Clifford group are unitary operators that map Pauli group to Pauli group by conjugation. For example, Hadamard operator $H$ is a Clifford operator because $$HIH^\dag=I,~HXH^\dag=Z,~HZH^\dag=X,~HYH^\dag=-Y,~iI=XYZ$$
- Pauli group is a subset of Clifford group. Because, by definition, a group maps onto itself.
- Any Clifford operator can be made from the operators $H,S,CX$. Here, $H$ is the Hadamard operator, $S$ is the $90\degree\!$-phase operator (i.e. $S^2=Z$) and $CX$ is the controlled not operation. In other words, these are the generators of the Clifford group:
$$\mathcal C = \big\langle H,S,CX \big\rangle$$
- Gottesman-Knill theorem states that any Clifford circuit starting with computation basis states and being measured in computational basis, can be efficiently simulated using classical computers. This is specially useful in stabilizer code.
- Clifford group does not constitute universal quantum gate sets. Universal quantum gate sets require $T$ gate along side Clifford gates.


## Proofs

### Theorem 1 
Any operator $(M)$ can be separated into Hermitian $(A)$ and anti-Hermitian $(iB)$ parts:
$$M = A+iB\text{\quad where\quad}A^\dag=A\text{~and~}B^\dag=B$$
> ### Proof
> Let $A=\frac{M+M^\dag}2$ and $B=\frac{M-M^\dag}{2i}$. Then,
$$
A^\dag = \left(\frac{M+M^\dag}2\right)^\dag
= \frac{M^\dag+(M^\dag)^\dag}2
= \frac{M^\dag+M}2
= A
$$
And, for the case of $B$,
$$
B^\dag = \left(\frac{M-M^\dag}{2i}\right)^\dag
= \frac{M^\dag-(M^\dag)^\dag}{-2i}
= \frac{M^\dag-M}{-2i}
= \frac{M-M^\dag}{2i}
= B
$$
In other words, both $A$ and $B$ are Hermitian. So, $iB$ is anti-Hermitian.

### Theorem 2
Hermitian and Anti-Hermitian parts of normal operators commute
$$N=A+iB\text{\quad where\quad}A^\dag=A,~B^\dag=B,~AB=BA$$
> ### Proof
> By [theorem 1](#theorem-1), $N=A+iB$ where $A^\dag=A,~B^\dag=B$. So we only need to show that they commute.
Since $N$ is a normal operator, 
$$\begin{align*}
&N^\dag N=NN^\dag\\
\implies& (A+iB)^\dag(A+iB) = (A+iB)(A+iB)^\dag\\
\implies& (A^\dag-iB^\dag)(A+iB) = (A+iB)(A^\dag-iB^\dag)\\
\implies& (A-iB)(A+iB) = (A+iB)(A-iB)\\
\implies& A^2+iAB-iBA+B^2 = A^2-iAB+iBA+B^2\\
\implies& 2i(AB-BA)=0\\
\implies& AB=BA.
\end{align*}$$

### Theorem 3
If $N$ is a normal operator, then for any state $\ket\psi$,
$$\Big\lVert N\!\ket\psi\Big\rVert = \Big\lVert N^\dag\!\ket\psi\Big\rVert$$
> ### Proof
> Since $N$ is a normal operator, $N^\dag N = NN^\dag$. Let the notation $\ket{N\psi}$ denote the state $N\!\ket\psi$. 
Then, $\bra{N\psi}=\left(N\!\ket\psi\right)^\dag
=\ket\psi^\dag\!N^\dag=\bra\psi\!N^\dag$. 
Similarly, $\ket{N^\dag\psi} = N^\dag\!\ket\psi$ and $\bra{N^\dag\psi} = \bra\psi\!N$.
That means,
$$\begin{align*}
\Big\lVert\ket{N\psi}\Big\rVert^2
&= \braket{N\psi|N\psi} = \bra\psi\!N^\dag N\!\ket\psi\\
&= \bra\psi\!NN^\dag\!\ket\psi\\
&= \braket{N^\dag\psi|N^\dag\psi}\\
&= \Big\lVert\ket{N^\dag\psi}\Big\rVert^2.
\end{align*}$$
Since, norm of a state is always non-negative, the norms must be equal.

### Theorem 4
If $N$ is a normal operator with an eigenvalue $\lambda$ and corresponding eigenvector $\ket{e}$, then $\ket{e}$ is also an eigenvector of $N^\dag$ with the eigenvalue $\lambda^*$,
$$N^\dag\!\ket{e} = \lambda^*\ket{e}$$
> ### Proof
> If $N$ is a normal operator, $M=(N-\lambda I)$ is also a normal operator *(where $I$ is the identity operator)*:
$$\begin{align*}
MM^\dag&=(N-\lambda I)(N-\lambda I)^\dag\\
&=(N-\lambda I)(N^\dag-\lambda^* I^\dag)\\
&=(N-\lambda I)(N^\dag-\lambda^* I)\\
&=NN^\dag-\lambda^*N-\lambda N^\dag+\lambda\lambda^* I\\
&=N^\dag N-\lambda^*N-\lambda N^\dag+\lambda\lambda^* I
&[\because N^\dag N = NN^\dag]\\
&=(N^\dag-\lambda^* I)(N-\lambda I)\\
&= M^\dag M
\end{align*}$$
So, by [theorem 3](#theorem-1), $\Big\lVert M\!\ket e \Big\rVert = \Big\lVert M^\dag\!\ket e \Big\rVert$. i.e.
$$
\Big\lVert (N-\lambda I)\ket e \Big\rVert 
= \Big\lVert (N^\dag-\lambda^* I)\ket e \Big\rVert 
$$
Now, let's focus on the eigenvector of $N$,
$$\begin{align*}
& N\!\ket e = \lambda\ket e\\
\implies & N\!\ket e = \lambda I\ket e\\
\implies & N\!\ket e -\lambda I\ket e = 0\\
\implies & (N-\lambda I)\ket e = 0\\
\implies & \Big\lVert (N-\lambda I)\ket e \Big\rVert = 0\\
\implies & \Big\lVert (N^\dag-\lambda^* I)\ket e \Big\rVert = 0\\
\implies & (N^\dag-\lambda^* I)\ket e = 0
&[\because \text{the only vector with norm 0 is the null vector}]\\
\implies & N^\dag\!\ket e -\lambda^* \ket e = 0\\
\therefore\quad & N^\dag\!\ket e = \lambda^* \ket e
\end{align*}$$
In other words, $\ket e$ is also an eigenvector of $N^\dag$ with the eigenvalue $\lambda^*$.


### Theorem 5
Let $N$ be a normal operator. Then its eigenvectors corresponding to *different* eigenvalues are orthogonal.
$$\braket{e_1|e_2}=0
\text{\quad where\quad}
\lambda_1\neq\lambda_2,~
N\!\ket{e_k}=\lambda_k\ket{e_k}$$
> ### Proof
> Let $\lambda_1$ and $\lambda_2$ be two distinct $(\lambda_1\neq\lambda_2)$ eigenvalues of $N$ and the corresponding eigenvectors be the $\ket{e_1}$ and $\ket{e_2}$.
Since $N$ is a normal operator, by [theorem 4](#theorem-4), $\ket{e_1}$ and $\ket{e_2}$ are also eigenvectors of $N^\dag$ with eigenvalues $\lambda_1^*$ and $\lambda_2^*$.
Now,
$$\begin{align*}
\lambda_2\braket{e_1|e_2} &= \bra{e_1}\lambda_2\ket{e_2}\\
&= \bra{e_1}N\!\ket{e_2} &[\because N\!\ket{e_2}=\lambda_2\ket{e_2}]\\
&= \left(\bra{e_1}N\right)\ket{e_2}\\
&= \left(N^\dag\ket{e_1}\right)^\dag\ket{e_2}\\
&= \left(\lambda_1^*\!\ket{e_1}\right)^\dag\ket{e_2} 
& [\because N^\dag\!\ket{e_1}=\lambda_1^*\ket{e_1}]\\
&= \ket{e_1}^\dag(\lambda_1^*)^\dag\ket{e_2}\\
&= \bra{e_1}\lambda_1\ket{e_2}\\
&= \lambda_1\braket{e_1|e_2}
\end{align*}$$
$\therefore (\lambda_1-\lambda_2)\braket{e_1|e_2} = 0$. 
Since $\lambda_1\neq\lambda_2$, it must be true that $\braket{e_1|e_2}=0.$

### Theorem 6 (*Spectral Theorem*)
Eigenvectors of a normal operator form an orthonormal basis. So a normal operator $N$ can be written as:
$$N=\sum_k \lambda_k \ket{e_k}\!\!\bra{e_k} \text{~where~}\braket{e_k|e_l}=\delta_{kl}$$
> ### Proof
> Let's split the theorem into two cases, the "non-degenerate" case (every eigenvalue is different) and the "degenerate" case (two or more eigenvalues are same).
***Case I: Non-degenerate eigenspace***
If any two eigenvalues of $N$ are different from each other, we can use [theorem 5](#theorem-5) to show that all the eigenvectors are orthogonal to each other. If $\ket{e_k}$ and $\ket{e_l}$ are eigenvectors of $N$ with corresponding eigenvalues $\lambda_k$ and $\lambda_k$ where $\lambda_k\neq\lambda_l$,
$$\braket{e_k|e_l}=0$$
If $k=l$, since $\ket{e_k}$ is normalized, $\braket{e_k|e_l}=1$. In other words, in the non-degenerate case, we have,
$$\braket{e_k|e_l}=\delta_{kl}$$
Now it is easy to see that the statement, $N=\sum_k \lambda_k \ket{e_k}\!\!\bra{e_k}$ must be true because,
$$N\!\ket{e_l}=\sum_k \lambda_k \ket{e_k}\!\!\braket{e_k|e_l}
=\sum_k\delta_{kl} \lambda_k \ket{e_k}
=\lambda_l\ket{e_l}$$
which is trivially true since $\ket{e_l}$ is an eigenvector of $N$ with the eigenvalue of $\lambda_l$.
***Case II: Degenerate eigenspace***
If we have same eigenvalue for some of the eigenvalues, then the corresponding eigenvectors need not be orthogonal. In fact we may have multiple degenerate eigenspace. 
However, in each degenerate case, we can use Gram-Schmidt orthogonalization process to find a set of orthonormal eigenvectors where $\braket{e_k|e_l}=\delta_{kl}$.
For the case of eigenspaces with different eigenvalues, by [theorem 5](#theorem-5), it is guaranteed that such eigenspaces are orthogonal to each other.
Thus we can find a complete orthonormal basis for the complex vector space. And each of this statevectors are eigenvectors of the operator $N$. So, following similar steps to the non-degenerate case, we can write,
$$N=\sum_k \lambda_k \ket{e_k}\!\!\bra{e_k} \text{~where~}\braket{e_k|e_l}=\delta_{kl}$$

### Theorem 7
Eigenvalues of unitary operators are of the form, $\lambda=e^{i\theta}$.
> ### Proof
> If $U$ is an unitary operator, by definition, $UU^\dag = I = U^\dag U$. Let $\lambda$ be an eigenvalue of $U$ and $\ket e$ be the corresponding eigenvector. Then,
$$\begin{align*}
& U^\dag U = I\\
\implies & \braket{e|U^\dag U|e} = \braket{e|I|e}\\
\implies & (U\!\ket e)^\dag(U\!\ket e) = \braket{e|e}
&[\because \bra e\!U^\dag = (U\!\ket e)^\dag]\\
\implies & (\lambda\!\ket e)^\dag(\lambda\!\ket e) = 1
& [\because U\!\ket e = \lambda\ket e]\\
\implies & (\ket e^\dag\lambda^\dag)(\lambda\!\ket e) = 1\\
\implies & (\bra e\!\lambda^*)(\lambda\!\ket e) = 1\\
\implies & \lambda\lambda^*\braket{e|e} = 1\\
\implies & \lambda\lambda^* = 1\\
\implies & |\lambda|^2 = 1\\
\implies & |\lambda| = 1\\
\therefore\quad& \lambda = e^{i\theta}
\end{align*}$$

### Theorem 8
Unitary operators preserve inner product between any two arbitrary states.
$$\braket{\psi'|\phi'}=\braket{\psi|\phi}\text{\quad where\quad}\ket{\psi'} = U\!\ket\psi\text{~and~}\ket{\phi'} = U\!\ket\phi$$
> ### Proof
> From the definition of $\ket{\psi'}$, we get, $\bra{\psi'} = \bra\psi\!U^\dag$.
If $U$ is an unitary operator, by definition, $UU^\dag = I = U^\dag U$. Then,
$$\begin{align*}
\braket{\psi'|\phi'}=\braket{\psi|U^\dag U|\phi} = \braket{\psi|I|\phi}=\braket{\psi|\phi}
\end{align*}$$


### Theorem 9
If $H$ is a Hermitian operator, $U=e^{iH}$ is an unitary operator.
> ### Proof
> Recall that, $e^A=\sum_n \frac{A^n}{n!}$ and $e^A e^B =e^{A+B}=e^B e^A$ if and only if $AB=BA$.
Here, 
$$U=e^{iH}=\sum_n \frac{(iH)^n}{n!}$$
Since, $H^\dag = H$,
$$U^\dag=\left(e^{iH}\right)^\dag
=\left(\sum_n \frac{(iH)^n}{n!}\right)^\dag
=\sum_n \frac{((iH)^\dag)^n}{n!}
=\sum_n \frac{(-iH)^n}{n!}
= e^{-iH}$$
Since the operators $iH$ and $-iH$ commute,
$$UU^\dag = e^{iH}e^{-iH} = e^{(iH-iH)}=e^0=I$$
Please note that the $0$ here is the zero operator. Similarly, we can show that, $U^\dag U=I$.
Thus $UU^\dag = I = U^\dag U$. So, U is indeed a unitary operator.


### Theorem 10
All the eigenvalues of Hermitian operators are real, $\lambda\in\mathbb R$.
> ### Proof
> Let $H$ be a Hermitian operator with an eigenvalue $\lambda$ and corresponding eigenvector $\ket e$.
Then by definition, $H^\dag=H$ and $H\!\ket e=\lambda\ket e$. Now,
$$\braket{e|H|e} = \braket{e|\lambda|e}=\lambda\braket{e|e}=\lambda$$
Again, by taking Hermitian conjugate of eigenvector definition, $\bra e\!H^\dag=\bra e\lambda^*$. Then,
$$\braket{e|H^\dag|e} = \braket{e|\lambda^*|e}=\lambda^*\braket{e|e}=\lambda^*$$
But for Hermitian operators, $H^\dag = H$. So,
$$\braket{e|H^\dag|e} = \braket{e|H|e}\implies\lambda^*=\lambda$$
Since $\lambda$ is its own complex conjugate, it must be a real number (imaginary part is zero).

### Theorem 11
If $H$ is a Hermitian operator, then for any state $\ket\psi$ in a Hilbert space, 
$$\braket{\psi|H|\psi} \in\mathbb R$$
> ### Proof
> By [spectral theorem](#theorem-6-spectral-theorem), all the eigenvalues $(\lambda_k)$  of $H$ are real and the corresponding eigenvectors $\ket{e_k}$ form an orthonormal basis for the Hilbert space.
So we may write $\ket\psi$ as a linear combination of the eigenvectors:
$$\begin{align*}
&\ket\psi = \sum_k\alpha_k\ket{e_k}\\
\implies & H\ket\psi = \sum_k\alpha_k H\ket{e_k}\\
\implies & H\ket\psi = \sum_k\alpha_k\lambda_k\ket{e_k}
& \Big[\because H\ket{e_k}=\lambda_k\ket{e_k}\Big]\\
\implies & \braket{\psi|H|\psi} = \left(\sum_j\alpha_j^*\bra{e_j}\right)
\left(\sum_k\alpha_k\lambda_k\ket{e_k}\right)
& \left[\because \bra\psi = \sum_k\alpha_k^*\bra{e_k}\right]\\
\implies & \braket{\psi|H|\psi} = \sum_j\sum_k
\alpha_j^*\bra{e_j}\alpha_k\lambda_k\ket{e_k}\\
\implies & \braket{\psi|H|\psi} = \sum_j\sum_k
\alpha_j^*\alpha_k\lambda_k\braket{e_j|e_k}\\
\implies & \braket{\psi|H|\psi} = \sum_j\sum_k
\alpha_j^*\alpha_k\lambda_k\delta_{jk}
&\Big[\because\braket{e_j|e_k}=\delta_{jk}\Big]\\
\implies & \braket{\psi|H|\psi} = 
\sum_k\alpha_k^*\alpha_k\lambda_k
&[\because j\neq k\iff\delta_{jk}=0]\\
\implies & \braket{\psi|H|\psi} = 
\sum_k\lambda_k|\alpha_k|^2
\end{align*}$$
Since, both $\lambda_k$ and $|\alpha_k|^2$ is real, $\braket{\psi|H|\psi}\in\mathbb R$.


### Theorem 12
All the eigenvalues of Positive semi-definite operators are real and positive, $\lambda\geq0$.

> ### Proof
> Since $S$ is also a Hermitian operator by definition, according to [theorem 10](#theorem-10), all eigenvalues must be real. Moreover, since for any state $\braket{\psi|S|\psi}\geq0$, taking $\ket\psi$ to be the eigenvector $\ket{e_k}$ of $S$ with eigenvalue $\lambda_k$, we get,
$$\begin{align*}
&\braket{e_k|S|e_k}\geq0\\
\implies&\braket{e_k|\lambda_k|e_k} \geq0 
& [\because S\ket{e_k} = \lambda_k\ket{e_k}]\\ 
\implies&\lambda_k\braket{e_k|e_k}  \geq0\\
&\therefore \lambda_k \geq0
& [\because\braket{e_k|e_k} = 1]
\end{align*}$$
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTkwNzIyODQ5OSw1NTA0MjYwNjQsMTQ2MD
I5OTg1MSwxMjk1NjcyMjAxLC0xMTQ1NjIxNzYzLDE5NTAyMjE0
NzQsLTE5NjIwMTc5MjVdfQ==
-->