#math #ml #dl 
# Scalars, Vectors, Matrices and Tensors
## Scalars
A scalar is just a single number, denoted by lowercase variable names.
## Vectors
A vector is an array of numbers.
We can think of vectors as identifying **points** in space, with each element giving the coordinate along a diﬀerent axis.
$$x = \begin{bmatrix}
x_1\\x_2\\\vdots\\x_n
\end{bmatrix}$$
## Matrices
A matrix is a **2-D** array of numbers, denoted by uppercase variable names ($\mathbf{A}$).
$$\mathbf{A} =\begin{bmatrix}
A_{1,1} & A_{1, 2} & \dots & A_{1, m}\\
A_{2, 1} & \dots & \dots & A_{2, m}\\
\vdots \\
A_{n, 1} & A_{n, 2} & \dots & A_{n, m}
\end{bmatrix}$$
in general, an element of a matrix is denoted by $A_{i,j}$ 
## Tensors
In the general case, an array of numbers arranged on a regular grid with a
variable number of axes is known as a tensor.

An element of a tensor $A$ at coordinates $(i, j, k)$ is denoted by $A_{i,j,k}$ 
# Transpose
$$
\mathbf{A} = \begin{bmatrix}
A_{1,1} & A_{1,2} \\
A_{2,1} & A_{2,2} \\
A_{3,1} & A_{3,2}
\end{bmatrix}
\Rightarrow
\mathbf{A}^\top = \begin{bmatrix}
A_{1,1} & A_{2,1} & A_{3,1} \\
A_{1,2} & A_{2,2} & A_{3,2}
\end{bmatrix}
$$
# Matrix addition
## With matrix
It requires both matrices to have the same shape. You simply add the corresponding elements: $C_{i,j} = A_{i,j} + B_{i,j}$.
$$
\begin{bmatrix}
A_{1,1} & A_{1,2} \\
A_{2,1} & A_{2,2}
\end{bmatrix} + 
\begin{bmatrix}
B_{1,1} & B_{1,2} \\
B_{2,1} & B_{2,2}
\end{bmatrix} =
\begin{bmatrix}
A_{1,1} + B_{1,1} & A_{1,2} + B_{1,2} \\
A_{2,1} + B_{2,1} & A_{2,2} + B_{2,2}
\end{bmatrix}
$$
## With Scalar
The scalar is added to each element of the matrix.
$$
\mathbf{A} + c = 
\begin{bmatrix}
A_{1,1} & A_{1,2} \\
A_{2,1} & A_{2,2}
\end{bmatrix} + c =
\begin{bmatrix}
A_{1,1} + c & A_{1,2} + c \\
A_{2,1} + c & A_{2,2} + c
\end{bmatrix}
$$
## With vector
In deep learning notation, adding a vector $\mathbf{b}$ to a matrix $\mathbf{A}$ (where $\mathbf{C} = \mathbf{A} + \mathbf{b}$) means the vector is added to each **row** of the matrix. This is known as **broadcasting**.
$$
\mathbf{C} = \mathbf{A} + \mathbf{b} \Rightarrow
\begin{bmatrix}
A_{1,1} & A_{1,2} \\
A_{2,1} & A_{2,2} \\
A_{3,1} & A_{3,2}
\end{bmatrix} + 
\begin{bmatrix}
b_1 \\
b_2
\end{bmatrix} =
\begin{bmatrix}
A_{1,1} + b_1 & A_{1,2} + b_2 \\
A_{2,1} + b_1 & A_{2,2} + b_2 \\
A_{3,1} + b_1 & A_{3,2} + b_2
\end{bmatrix}
$$
# Multiplication
## Scalar
$D_{i,j} = s \cdot B_{i,j}$
$$
s \cdot \begin{bmatrix}
A_{1,1} & A_{1,2} \\
A_{2,1} & A_{2,2}
\end{bmatrix} =
\begin{bmatrix}
s A_{1,1} & s A_{1,2} \\
s A_{2,1} & s A_{2,2}
\end{bmatrix}
$$
## Hadamard Product (Element-wise product)
$\mathbf{A} \odot \mathbf{B}$ where $C_{i,j} = A_{i,j} B_{i,j}$
$$
\mathbf{A} \odot \mathbf{B} = 
\begin{bmatrix}
A_{1,1} & A_{1,2} \\
A_{2,1} & A_{2,2}
\end{bmatrix} \odot
\begin{bmatrix}
B_{1,1} & B_{1,2} \\
B_{2,1} & B_{2,2}
\end{bmatrix} =
\begin{bmatrix}
A_{1,1}B_{1,1} & A_{1,2}B_{1,2} \\
A_{2,1}B_{2,1} & A_{2,2}B_{2,2}
\end{bmatrix}
$$
## Vector (Dot product)
Please read [Understanding dot product](#^649259)
$\mathbf{x} \cdot \mathbf{y} = \mathbf{x}^\top \mathbf{y} = \sum_i x_i y_i$
$$
\mathbf{x}^\top \mathbf{y} = 
\begin{bmatrix} x_1 & x_2 & x_3 \end{bmatrix}
\begin{bmatrix} y_1 \\ y_2 \\ y_3 \end{bmatrix} = 
x_1 y_1 + x_2 y_2 + x_3 y_3
$$
	## Matrix-Vector Product
$$
\mathbf{A}\mathbf{x} = \begin{bmatrix}
A_{1,1} & A_{1,2} \\
A_{2,1} & A_{2,2} \\
A_{3,1} & A_{3,2}
\end{bmatrix}
\begin{bmatrix}
x_1 \\
x_2
\end{bmatrix} =
\begin{bmatrix}
A_{1,1}x_1 + A_{1,2}x_2 \\
A_{2,1}x_1 + A_{2,2}x_2 \\
A_{3,1}x_1 + A_{3,2}x_2
\end{bmatrix}
$$
This is the most important notation for deep learning. It shows that the result is just the columns of $\mathbf{A}$ scaled by the elements of $\mathbf{x}$. ^a93b9a
$$
\mathbf{A}\mathbf{x} = x_1 \begin{bmatrix}
A_{1,1} \\
A_{2,1} \\
A_{3,1}
\end{bmatrix} + 
x_2 \begin{bmatrix}
A_{1,2} \\
A_{2,2} \\
A_{3,2}
\end{bmatrix} =
\sum_{j} x_j \mathbf{A}_{:,j}
$$
where $\mathbf{A}_{:,j}$ refers to the $j$-th column of matrix $\mathbf{A}$.

# Linear combination
In simple terms, a **linear combination** is a fancy way of saying add/multiply/subtract 2 or more vectors. i.e. for $v_1 + v_2 = v_3$ , $v_3$ is said to a linear combination of $v_1$ and $v_2$.
# Span of matrix
If you think of each column as a "direction" you are allowed to move in, the span is every single point in space you can reach using only those directions.
If a matrix $\mathbf{A}$ has columns $\mathbf{v}_1, \mathbf{v}_2, \dots, \mathbf{v}_n$, the span is written as:
$$
\text{span}(\mathbf{A}) = \{ x_1 \mathbf{v}_1 + x_2 \mathbf{v}_2 + \dots + x_n \mathbf{v}_n \mid x_1, \dots, x_n \in \mathbb{R} \}
$$
Imagine a $3 \times 2$ matrix:

$$\mathbf{A} = \begin{bmatrix} 1 & 0 \\ 0 & 1 \\ 0 & 0 \end{bmatrix}$$

- **Column 1:** Allows you to move along the x-axis.
- **Column 2:** Allows you to move along the y-axis.
- **The Span:** Since there is no way to move in the z-direction (the third row is all zeros), the span of this matrix is the **2D $xy$-plane** sitting inside 3D space.

# Norms

The norm of a vector $x$ measures the distance from the origin to the point $x$.
They are functions mapping vectors to non-negative values.

## Lp norm

$$
\| \mathbf{x} \|_p = \left( \sum_i |x_i|^p \right)^{\frac{1}{p}}
$$ 
where $p$ = no of dimensions in vector space.

For a function to be a **norm**, it must satisfy:
1. **$f(\mathbf{x}) = 0 \implies \mathbf{x} = 0$** (Only the zero vector has zero length).
2. **$f(\mathbf{x} + \mathbf{y}) \le f(\mathbf{x}) + f(\mathbf{y})$** (The Triangle Inequality).
> Implies that the vector $\mathbf{x + y}$ has length $\leq$ length of vector $\mathbf{x}$ + length of vector $\mathbf{y}$
3. **$\forall \alpha \in \mathbb{R}, f(\alpha \mathbf{x}) = |\alpha| f(\mathbf{x})$** (Absolute scalability).

## Manhattan Norm / L1 norm

Used when the difference between exactly zero and "small but non-zero" is important (often for **Sparsity**).
$\| \mathbf{x} \|_1 = \sum_i |x_i|$

>[!example] Example
>If x = [3, −4, 2], then the L1 norm is:
> $\| \mathbf{x} \|_1 = |3| + |-4| + |2| = 3 + 4 + 2 = 9$

## L2 norm / Euclidean Norm

It is the straight-line distance from the origin.
$\|\mathbf{x}\|_2 = \sqrt{x_1^2 + x_2^2 + \dots + x_n^2}$



>[!example] Example
>If x = [3, −4, 2], then the L2 norm is:
> $\| \mathbf{x} \|_2 = \sqrt{3^2 + (-4)^2 +2^2} \approx 5.39$

In terms of matrices:
$$\|\mathbf{x}\|^2_2 = \mathbf{x}^T \mathbf{x}$$ ^e5914d

>[!info]- Proof
>If $\mathbf{x} = \begin{bmatrix} x_1 \\ x_2 \end{bmatrix}$, then its transpose is $\mathbf{x}^T = \begin{bmatrix} x_1 & x_2 \end{bmatrix}$.
>$$\mathbf{x}^T \mathbf{x} = \begin{bmatrix} x_1 & x_2 \end{bmatrix} \begin{bmatrix} x_1 \\ x_2 \end{bmatrix} = (x_1 \cdot x_1) + (x_2 \cdot x_2) = x_1^2 + x_2^2 =\|\mathbf{x}\|^2_2 $$

## L-infinity norm / Max Norm

It measures the "size" of a vector by taking the largest absolute value among its components.
$\|\mathbf{x}\|_{\infty} = \max_i(|x_i|)$

>[!example] Example
>If x = [3, −4, 2], then the L-infinity norm is:
> $\| \mathbf{x} \|_{\infty} = \max(|3|, |-4|, |2|) = 4$

## Frobenius Norm

This is used to measure the size of a **matrix** rather than a vector. It is the matrix equivalent of the $L^2$ norm.
$\|\mathbf{A}\|_F = \sqrt{\sum_{i,j} A_{i,j}^2}$

| **Norm**       | **Usage in ML**                       | **Geometry** |
| -------------- | ------------------------------------- | ------------ |
| **$L^1$**      | Feature selection / Sparsity          | Diamond      |
| **$L^2$**      | General weight regularization (Ridge) | Circle       |
| **$L^\infty$** | Sensitivity analysis                  | Square       |
| **Frobenius**  | Measuring "energy" in a weight matrix | -            |

# [Basis Vectors](https://www.geeksforgeeks.org/machine-learning/basis-vectors-in-linear-algebra-ml/)
The smallest possible set of vectors (i.e. directions) that 
1. covers the entire vector space 
	The set of vectors can be scaled to represent any vector in the vector space.
	Ex: $1.5\hat{i} + 2\hat{j}$ 
2. All vectors in the set are linearly independent i.e. no vector in the set can be created by combining the others.
Example: For 2D space, the basis vectors are $$v_1=\begin{bmatrix}1\\0
\end{bmatrix},\space\space v_2=\begin{bmatrix}0\\1
\end{bmatrix}$$ i.e. $\hat{i}$ and $\hat{j}$ .
You could also use these two vectors as a basis for $\mathbb{R}^2$:

$$\mathbf{v}^{(1)} = \begin{bmatrix} 1 \\ 1 \end{bmatrix}, \quad \mathbf{v}^{(2)} = \begin{bmatrix} -1 \\ 1 \end{bmatrix}$$

They still span the whole plane and aren't redundant, they just represent a "tilted" coordinate system.

## Identity matrix
The columns of an **Identity Matrix** ($\mathbf{I}$) represent the standard basis for that dimension.
**Example for $\mathbb{R}^3$:**

$$\mathbf{I}_3 = \begin{bmatrix} 1 & 0 & 0 \\ 0 & 1 & 0 \\ 0 & 0 & 1 \end{bmatrix}$$

Each column is a basis vector pointing perfectly along one axis ($x, y,$ or $z$).
# Orthogonal Vectors
$\mathbf{x}$ and $\mathbf{y}$ are orthogonal vectors if $\mathbf{x^{T}y} = 0$ i.e they are **perpendicular** to each other.
> [!note] _n-dimensional_ space has _n_ mutually orthogonal vectors
## Orthonormal Vectors
$\mathbf{x}$ and $\mathbf{y}$ are orthonormal vectors iff:
1. They are orthogonal to each other
2. Both have unit norms
# Rank of matrix
The rank of matrix $\mathbf{A}$, denoted as $\text{rank}(\mathbf{A})$, is the number of **linearly independent** columns in the matrix.

A set of vectors $\{\mathbf{v}^{(1)}, \dots, \mathbf{v}^{(n)}\}$ is **linearly independent** if the only way to sum them to zero (using scalar coefficients $c_i$) is to make every single coefficient zero. ^e3fd32
$$
c_1 \mathbf{v}^{(1)} + c_2 \mathbf{v}^{(2)} + \dots + c_n \mathbf{v}^{(n)} = \mathbf{0} \implies c_1 = c_2 = \dots = c_n = 0
$$
If a matrix is $m \times n$, its **maximum** possible rank is the smaller of the two numbers ($\min(m, n)$).
Example A: Full Rank (Rank 2)
The columns point in different directions.

$$\mathbf{A} = \begin{bmatrix} 1 & 0 \\ 0 & 1 \\ 0 & 0 \end{bmatrix} \implies \text{rank}(\mathbf{A}) = 2$$
_The span is a 2D plane._ All the columns and rows in the matrix are different from each other so $\text{rank}(\mathbf{A}) = \text{min}(2, 3) = 2$ 

$$v_1 = \begin{bmatrix}1 \\ 0 \\ 0\end{bmatrix}\implies \mathbf{A_{:,1}}
\space\space v_2=\begin{bmatrix}0 \\ 1 \\ 0\end{bmatrix} \implies \mathbf{A_{:,2}}
$$
The area enclosed between the 2 vectors is the _span_.
![Pasted image 20260225210147](Assets/Pasted%20image%2020260225210147.png)
Example B: Rank Deficient (Rank 1)
The second column is just the first column multiplied by 2. It adds no "new" direction.

$$\mathbf{B} = \begin{bmatrix} 1 & 2 \\ 2 & 4 \\ 3 & 6 \end{bmatrix} \implies \text{rank}(\mathbf{B}) = 1$$

_The span is just a 1D line, even though the matrix has two columns._
$$v_1 = \begin{bmatrix}1 \\ 2 \\ 3\end{bmatrix}\implies \mathbf{B_{:,1}} (\text{speck of orange in the graph})
\space\space v_2=\begin{bmatrix}2 \\ 4 \\ 6\end{bmatrix} \implies \mathbf{B_{:,2}}
$$
![Pasted image 20260225210901](Assets/Pasted%20image%2020260225210901.png)

# Inverse of matrix
It can be calculated only if:
1. Matrix is square
2. All columns are linearly independent
To calculate the inverse, we can use the property :$$AA^{-1} = I$$
# System of linear equations
Suppose you have the following system of two equations:
1. $2x_1 + 3x_2 = 8$
2. $5x_1 - 1x_2 = 3$
We can denote this in matrix-vector notation as **$\mathbf{Ax} = \mathbf{b}$ where:
- $\mathbf{A} \in \mathbb{R}^{m \times n}$: A **known matrix** of coefficients (the constraints).
- $\mathbf{b} \in \mathbb{R}^{m}$: A **known vector** of constants.
- $\mathbf{x} \in \mathbb{R}^{n}$: A **vector of unknown variables** we want to solve for.
$$
\begin{bmatrix}
2 & 3 \\
5 & -1
\end{bmatrix}
\begin{bmatrix}
x_1 \\
x_2
\end{bmatrix}
=
\begin{bmatrix}
8 \\
3
\end{bmatrix}
$$
Depending on the [span](#Span%20of%20matrix) of the columns of $\mathbf{A}$ , the system can have:
1. **Exactly one solution:** The columns are linearly independent and cover the space where $\mathbf{b}$ lives. (Above example)
2. **No solution:** $\mathbf{b}$ lies outside the span of the columns of $\mathbf{A}$.
>[!example] Example
>$$\mathbf{A} = \begin{bmatrix} 1 & 0 \\ 0 & 1 \\ 0 & 0 \end{bmatrix}, \quad \mathbf{b} = \begin{bmatrix} 1 \\ 1 \\ 5 \end{bmatrix}$$
**The Span:** The columns of $\mathbf{A}$ only span the **$xy$-plane** ($z=0$).	**The Conflict:** The vector $\mathbf{b}$ is "floating" 5 units above that plane.
**Result:** No matter how you scale or add the columns of $\mathbf{A}$, you can never get a $z$-value of 5. The system is **inconsistent**.
3. **Infinitely many solutions:** There is redundancy (linear dependence) in the columns.
>[!example] Example 
>$$\mathbf{A} = \begin{bmatrix} 1 & 2 \\ 1 & 2 \end{bmatrix}, \quad \mathbf{b} = \begin{bmatrix} 3 \\ 3 \end{bmatrix}$$
**The Problem:** The second column is just $2 \times$ the first column. They are **linearly dependent**.
You want to solve $x_1 \begin{bmatrix} 1 \\ 1 \end{bmatrix} + x_2 \begin{bmatrix} 2 \\ 2 \end{bmatrix} = \begin{bmatrix} 3 \\ 3 \end{bmatrix}$.
>
One way: $x_1 = 3, x_2 = 0$. 
Another way: $x_1 = 1, x_2 = 1$.
 Another way: $x_1 = -1, x_2 = 2$.
 >
 Because you can "trade-off" between $x_1$ and $x_2$ without changing the direction, there are **infinitely many combinations** that work.
## Solving for exactly one solution
If the matrix $\mathbf{A}$ is square and non-singular (all columns are [linearly independent](#^e3fd32)):
$$
\begin{aligned}
\mathbf{Ax} &= \mathbf{b} \\
\mathbf{A}^{-1}\mathbf{Ax} &= \mathbf{A}^{-1}\mathbf{b} \\
\mathbf{x} &= \mathbf{A}^{-1}\mathbf{b}
\end{aligned}
$$
> [!warning] Implementation Note
>
> While mathematically elegant, the [Deep Learning Book](https://www.deeplearningbook.org/contents/linear_algebra.html) explicitly states on page 35 that $\mathbf{A}^{-1}$ should **not be used in practice** for most software applications. Because of limited precision on digital computers, algorithms like **Gaussian Elimination** or **LU Decomposition** provide more accurate results.

# Special Matrices

## Diagonal Matrix
A **diagonal matrix** $\mathbf{D}$ is a matrix where all entries are zero except for those on the main diagonal (from the top left to the bottom right.
$$\mathbf{D} = \begin{bmatrix} d_1 & 0 & 0 \\ 0 & d_2 & 0 \\ 0 & 0 & d_3 \end{bmatrix} = \text{diag}(v) \space\space\text{where}\space v=\begin{bmatrix}d_1\\d_2\\d_3\end{bmatrix}$$
i.e. a diagonal matrix can be represented as a vector of all the non-zero elements.
Thus, $\mathbf{Dx} = v\odot \mathbf{x}$ where $\mathbf{x}$ is a vector.  
$$\begin{bmatrix} 3 & 0 \\ 0 & 0.5 \end{bmatrix} \begin{bmatrix} 1 \\ 1 \end{bmatrix}  = \begin{bmatrix} 3 \\ 0.5 \end{bmatrix} = 
\begin{bmatrix} 3 \\ 0.5 \end{bmatrix}\begin{bmatrix} 1 \\ 1 \end{bmatrix}$$
Diagonal matrices are preferred in large-scale machine learning for three main reasons:
+ **Fast Inversion:** To find $\mathbf{D}^{-1}$, you simply take the reciprocal of each diagonal element.

$$\mathbf{D}^{-1} = \text{diag}(1/d_1, 1/d_2, \dots, 1/d_n)$$

_(Note: This only works if no diagonal element is zero.)_
- **Fast Multiplication:** Multiplying $\mathbf{Dx}$ only requires $O(n)$ operations, whereas a full matrix requires $O(n^2)$.
- **Powers:** To calculate $\mathbf{D}^{100}$, you just raise each diagonal entry to the power of 100. Doing this with a normal matrix would take massive computing power.

**Nonsquare** diagonal matrices can also be multiplied cheaply. When you multiply a vector $\mathbf{x}$ by a non-square diagonal matrix $\mathbf{D}$, you are scaling the elements $x_i$ by the diagonal values $d_i$, and then either **padding with zeros** (if the matrix is tall) or **truncating/discarding elements** (if the matrix is wide).

A _tall_ matrix i.e. no of rows > no of columns, represents a transformation from a lower-dimensional space to a higher-dimensional one.
>[!example] Example
>Moving from $\mathbb{R}^2$ (2D) to $\mathbb{R}^3$ (3D). Let $\mathbf{D}$ be a $3 \times 2$ matrix and $\mathbf{x}$ be a 2D vector.
>$$\begin{bmatrix} 7 & 0 \\ 0 & 2 \\ 0 & 0 \end{bmatrix} \begin{bmatrix} x_1 \\ x_2 \end{bmatrix} = \begin{bmatrix} 7x_1 \\ 2x_2 \\ 0 \end{bmatrix}$$

A _wide_ matrix i.e. no of columns > no of rows,  represents a transformation from a higher-dimensional space to a lower-dimensional one.
>[!example] Example
>Moving from $\mathbb{R}^3$ (3D) to $\mathbb{R}^2$ (2D). Let $\mathbf{D}$ be a $3 \times 2$ matrix and $\mathbf{x}$ be a 3D vector.
>$$\begin{bmatrix} 4 & 0 & 0 \\ 0 & 5 & 0 \end{bmatrix} \begin{bmatrix} x_1 \\ x_2 \\ x_3 \end{bmatrix} = \begin{bmatrix} 4x_1 \\ 5x_2 \end{bmatrix}$$
## Orthogonal Matrix
A square matrix A is orthogonal if:
$$A^T A = AA^T = I \implies A^{-1} = A^T$$
A orthogonal matrix satisfies two conditions:
1. **Orthogonality:** Every column is perpendicular to every other column (their dot product is 0).
2. **Normalization:** Every column has a length ([norm](#Norms)) of exactly 1.

Example: Consider this matrix $A$:

$$A = \begin{bmatrix} 0 & 1 \\ -1 & 0 \end{bmatrix}$$

If we check $A^T A$:

$$\begin{bmatrix} 0 & -1 \\ 1 & 0 \end{bmatrix} \begin{bmatrix} 0 & 1 \\ -1 & 0 \end{bmatrix} = \begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix}$$

Orthogonal matrices are valued as they:
1. **Preserve geometry**: If you multiply a vector by an orthogonal matrix, , you might rotate or flip it, but you won't stretch or squash it. i.e. it preserves the length and the angle between them.
>[!info]- Proof
> **Preserves length**
> Given a vector $\mathbf{x}$ and a orthogonal matrix $Q$, we want to show that $\|Q\mathbf{x}\|^2 =\|\mathbf{x}\|^2$
> 
>[Recall that ](#^e5914d): $\|\mathbf{x}\|^2 = \mathbf{x}^T \mathbf{x}$. Therefore:
>$$\|Q\mathbf{x}\|^2 = (Q\mathbf{x})^T (Q\mathbf{x})$$
>Using the property of transposes $(AB)^T = B^T A^T$:
$$\|Q\mathbf{x}\|^2 = \mathbf{x}^T (Q^T Q) \mathbf{x}$$
Since $Q$ is orthogonal, $Q^T Q = I$:
$$\|Q\mathbf{x}\|^2 = \mathbf{x}^T I \mathbf{x} = \mathbf{x}^T \mathbf{x} = \|\mathbf{x}\|^2$$
>
>**Preserves angle**
>The angle between two vectors $\mathbf{x}$ and $\mathbf{y}$ is determined by their dot product. For an orthogonal matrix $Q$:
$$(Q\mathbf{x}) \cdot (Q\mathbf{y}) = (Q\mathbf{x})^T (Q\mathbf{y})$$
> ([Recall that](#Vector%20(Dot%20product)): $\mathbf{x}\cdot\mathbf{y}=\mathbf{x}^\top\mathbf{y}$) 
> 
>Using the property of transposes $(AB)^T = B^T A^T$:
>$$\mathbf{x}^T Q^T Q \mathbf{y} = \mathbf{x}^T I \mathbf{y} = \mathbf{x}^T \mathbf{y}$$
>(For an orthogonal matrix $Q$: $Q^T Q = I$) 

2. **Computational Efficiency**: Since $A^{-1} = A^T$ for an orthogonal matrix, calculating inverse is nearly instantaneous.
3. **Numerical Stability:** Because they don't change the magnitude of the data, they prevent "exploding" or "vanishing" values during complex iterative calculations.
## Symmetric matrix
A square matrix $A$ is symmetric if it is equal to its own transpose:
$$A = A^T$$
**Inverse**: If it exists, $A^{-1}$ is also symmetric.
**Powers**: $A^k$ is also symmetric.
>[!important]
>For any non-square matrix $A$, $AA^T$ and $A^TA$ are symmetric
> Let $A= \begin{bmatrix}
3 & 2 \\
1 & 4 \\
0 & -1 
\end{bmatrix}$
>
> Then $A^T = \begin{bmatrix}
3 & 1 & 0 \\
2 & 4 & -1 \\
\end{bmatrix}$
> $$AA^T = \begin{bmatrix}
3 & 2 \\
1 & 4 \\
0 & -1 
\end{bmatrix}\begin{bmatrix}
3 & 1 & 0 \\
2 & 4 & -1 \\
\end{bmatrix} = \begin{bmatrix}
13 & 11 &-2 \\
11 & 17&-4 \\
-2 & -4& 1 
\end{bmatrix}$$
>
>If $A$ is a $m\times n$ matrix then $A^T$ is a $n\times m$ matrix, then their multiplication will either be $m\times m$ or a $n\times n$ matrix.
# Determinant of Matrix
The **determinant** of a **square** matrix is a single number that summarizes how much that matrix "scales" the space it acts upon. 
If the matrix is the transformation, the determinant is the **expansion factor**.

If you have a unit square with an area of 1:
- If $\det(A) = 3$, the transformed shape will have an **area of 3**.
- If $\det(A) = 0.5$, the shape is compressed to **half its area**.

>If $\text{det}(A) = 0$ :
>1. the matrix "squashes" the entire space into a lower dimension (e.g., squashing a 2D plane into a 1D line). 
>2. The matrix is not invertible i.e. $A^{-1}$ does not exist.

>If $\text{det}(A)<0$ :
>- The space was scaled by a factor of 2.
>- The space was **flipped** (inverted). Think of it like looking at a shape in a mirror or flipping a piece of paper over. The "orientation" has changed.

If we have matrix $A$:
$$A = \begin{bmatrix} a & b & c \\ d & e & f \\ g & h & i \end{bmatrix}$$
$$\det(A) = a \begin{vmatrix} e & f \\ h & i \end{vmatrix} - b \begin{vmatrix} d & f \\ g & i \end{vmatrix} + c \begin{vmatrix} d & e \\ g & h \end{vmatrix}$$
$$ = a(ei - fh) - b(di - fg) + c(dh - eg)$$
# Matrix Decomposition
Integers can be decomposed into prime factors. The way we represent the number 12 will change depending on whether we write it in base ten or in binary, but it will always be true that 12 = 2×2×3. From this representation, we can conclude useful properties, for example, that 12 is not divisible by 5, and that any integer multiple of 12 will be divisible by 3.

Similarly, we can also decompose matrices in ways that show us information about their functional properties that is not obvious from the representation of the matrix as an array of elements.

## Eigen Decomposition
>[!warning] Eigen decomposition exists only for **square** matrices!

$$A\mathbf{v} = \lambda\mathbf{v}$$
Most vectors change direction when multiplied by a matrix. However, an **eigenvector** ($\mathbf{v}$) is a special vector that only gets "stretched" or "squashed" by a scale factor called the **eigenvalue** ($\lambda$). i.e.  ^0d4213
- **Direction:** Stays the same.    
- **Magnitude:** Changes by a factor of $\lambda$.
## Finding A
If a square matrix $A$ has $n$ linearly independent eigenvectors, we can bundle them all together into a single equation:$$A = V \Lambda V^{-1}$$
where,
- **$V$:** A matrix where each column is an **eigenvector** of $A$. $$V = \begin{bmatrix} | & | & & | \\ \mathbf{v}_1 & \mathbf{v}_2 & \cdots & \mathbf{v}_n \\ | & | & & | \end{bmatrix}$$
- **$\Lambda$ (Lambda):** A diagonal matrix containing the corresponding **eigenvalues**. $$\Lambda = \begin{bmatrix} \lambda_1 & 0 & \cdots & 0 \\ 0 & \lambda_2 & \cdots & 0 \\ \vdots & \vdots & \ddots & \vdots \\ 0 & 0 & \cdots & \lambda_n \end{bmatrix}$$
- **$V^{-1}$:** The inverse of the eigenvector matrix.

> **Tip:** For **real symmetric matrices** (common in ML), the eigenvectors are [orthonormal](https://www.deeplearningbook.org/contents/linear_algebra.html), meaning $V^{-1} = V^T$. This makes the formula $A = V \Lambda V^T$.

>[!example] Example
>Let’s look at a simple matrix $A$ that scales the x-axis by 3 and the y-axis by 2:
> $$A = \begin{bmatrix} 3 & 0 \\ 0 & 2 \end{bmatrix}$$
> Since it is already diagonal, its eigenvectors are the standard basis vectors $\begin{bmatrix} 1 \\ 0 \end{bmatrix}$ and $\begin{bmatrix} 0 \\ 1 \end{bmatrix}$, and its eigenvalues are $3$ and $2$. The decomposition looks like this:
$$\underbrace{\begin{bmatrix} 3 & 0 \\ 0 & 2 \end{bmatrix}}_{A} = \underbrace{\begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix}}_{V} \underbrace{\begin{bmatrix} 3 & 0 \\ 0 & 2 \end{bmatrix}}_{\Lambda} \underbrace{\begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix}}_{V^{-1}}$$

One application of this is to calculate powers of $A$ :
$$A^k = (V \Lambda V^{-1})^k=V \Lambda V^{-1}V \Lambda V^{-1}\dots V \Lambda V^{-1}$$
We know that: $AA^{-1}=A^{-1}A=I$ , so we can rewrite above eqn as:
$$A^k = V \Lambda I \Lambda I\dots I\Lambda V^{-1} = V\Lambda^kV^{-1}$$
Instead of multiplying $A$ by itself 100 times, you just raise the numbers on the diagonal of $\Lambda$ to the power of $k$.
The no of matrix multiplications has reduced from $k$ to 2. 
## Finding eigenvalues
$$A\mathbf{v} = \lambda\mathbf{v}\implies A\mathbf{v} -\lambda\mathbf{v} = 0 \implies (A - \lambda I)\mathbf{v} = 0$$
To satisfy this equation: either $\mathbf{v} = 0$ or $A-\lambda I= 0$ . Therefore, to find a eigenvector, we must assume $A-\lambda I = 0$. 
Since  $A-\lambda I = 0$ , this implies $\det(A-\lambda I) = 0$  ([ A zero vector has zero area](#Determinant%20of%20Matrix))
So to find the value of $\lambda$ , we solve for $\det(A-\lambda I) = 0$

>[!example] Example
>Let $A = \begin{bmatrix} 2 & -4 \\ -1 & -1 \end{bmatrix}$.
>$$A - \lambda I = \begin{bmatrix} 2 & -4 \\ -1 & -1 \end{bmatrix} - \lambda\begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix} =  \begin{bmatrix} 2-\lambda & -4 \\ -1 & -1-\lambda \end{bmatrix}$$
>$$\det \begin{bmatrix} 2-\lambda & -4 \\ -1 & -1-\lambda \end{bmatrix} = (2-\lambda)(-1-\lambda) - (-4)(-1) = 0$$
>$$\lambda^2 - \lambda - 6 = 0 \implies (\lambda - 3)(\lambda + 2) = 0$$
**Eigenvalues**: $\lambda_1 = 3, \lambda_2 = -2$.
## Finding eigenvectors
After finding eigenvalues, we can substitute any $\lambda$ value in the equation: $(A - \lambda I)\mathbf{v} = 0$ and solve the resulting [#System of linear equations](#System%20of%20linear%20equations) to find the eigenvector.

>[!example] Example
>Let $A = \begin{bmatrix} 2 & -4 \\ -1 & -1 \end{bmatrix}, \lambda_1 = 3, \lambda_2 = -2$
>$$(A - \lambda I)\mathbf{v} = 0$$
>$$\begin{bmatrix} 2 & -4 \\ -1 & -1 \end{bmatrix} - \lambda\begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix}\mathbf{v} = \begin{bmatrix}0 \\ 0\end{bmatrix}$$
>
>Substituting $\lambda_1 = 3$ :
>$$\begin{bmatrix} 2-3 & -4 \\ -1 & -1-3 \end{bmatrix} \begin{bmatrix} x_1 \\ x_2 \end{bmatrix} = \begin{bmatrix} 0 \\ 0 \end{bmatrix} \implies \begin{bmatrix} -1 & -4 \\ -1 & -4 \end{bmatrix} \begin{bmatrix} x_1 \\ x_2 \end{bmatrix} = \begin{bmatrix} 0 \\ 0 \end{bmatrix}$$
>Both rows simplify to $-x_1 - 4x_2 = 0$, or $x_1 = -4x_2$.
>If we let $x_2 = 1$, then $x_1 = -4$.
>**Eigenvector**: $v = \begin{bmatrix} -4 \\ 1 \end{bmatrix}$ (or any multiple).
>
>Substituting $\lambda_1 = -2$ :
>$$\begin{bmatrix} 2+2 & -4 \\ -1 & -1+2 \end{bmatrix} \begin{bmatrix} x_1 \\ x_2 \end{bmatrix} = \begin{bmatrix} 0 \\ 0 \end{bmatrix} \implies \begin{bmatrix}4 & -4 \\ -1 & 1 \end{bmatrix} \begin{bmatrix} x_1 \\ x_2 \end{bmatrix} = \begin{bmatrix} 0 \\ 0 \end{bmatrix}$$
>Both rows simplify to $4x_1 - 4x_2 = 0$, and $-x_1 + x_2 = 0$. which is $x_1=x_2$
>If we let $x_2 = 1$, then $x_1 =1$.
>**Eigenvector**: $v = \begin{bmatrix} 1 \\ 1 \end{bmatrix}$ (or any multiple).
>
## Singular Value Decomposition (SVD)
>[!info] This works for both square and non-square matrices.
>

SVD decomposes an $m \times n$ matrix $A$ into three parts:
$$A = U \Sigma V^T$$
where,
$U$ = $m\times m$ [#Orthogonal Matrix](#Orthogonal%20Matrix). 
	Its columns are [eigenvectors](#^0d4213) of $AA^T$ , also called **left singular vectors**.
	It represents a rotation in the _output_ space
$\sum$ = $m\times n$ [#Diagonal Matrix](#Diagonal%20Matrix) . 
	The non-zero entries are the **square roots** of the [eigenvalues](#^0d4213) of $A^TA$.
	It represents a linear transform.
$V^T$ = $n\times n$ orthogonal matrix.
	Its columns are [eigenvectors](#^0d4213) of $A^TA$ , also called **right singular vectors**
		It represents a rotation in the _input_ space

### **Understanding the formula**
Matrix decomposition is about dividing a matrix into its fundamental parts, where **each part is independent from the other parts**.

A linear transform can stretch, compress or reflect a vector or shape, but it does not necessarily do so equally in all dimensions, leading to a ___sheared___ or ___tilted___ output.

Lets say we have a linear transform $A = \begin{bmatrix} 1 & 1 \\ 0 & 1 \end{bmatrix}$ and a unit square defined by the vectors $x = \begin{bmatrix} 1 \\ 0 \end{bmatrix}$ (horizontal) and $y = \begin{bmatrix} 0 \\ 1 \end{bmatrix}$. 

Using this [notation](#^a93b9a), we can say that:
$$A\mathbf{x} = Ax + Ay = \begin{bmatrix} 1 & 1 \\ 0 & 1 \end{bmatrix}\begin{bmatrix} 1 \\ 0 \end{bmatrix} +\begin{bmatrix} 1 & 1 \\ 0 & 1 \end{bmatrix}\begin{bmatrix} 0 \\ 1 \end{bmatrix}$$
$$=\begin{bmatrix} 1 \\ 0 \end{bmatrix} + \begin{bmatrix} 1 \\ 1 \end{bmatrix}$$
As we can see from the answer as well as the image below: after transform, the $y$ component has changed to $\begin{bmatrix} 1 \\ 1 \end{bmatrix}$ which implies the new $y$ vector is **not independent** of the new $x$ vector.

If the axes are ___tilted___ i.e. not perpendicular to each other, the _importance_ or _contribution_ of each dimension gets mixed together, which makes it hard to 
understand exactly what "action" (compress / stretch / reflect) the transform $A$ is performing in each dimension.

|           Before transform           |           After transform            |
| :----------------------------------: | :----------------------------------: |
| ![Pasted image 20260228165219](Assets/Pasted%20image%2020260228165219.png) | ![Pasted image 20260228165358](Assets/Pasted%20image%2020260228165358.png) |
|                                      |                                      |
To understand how SVD decomposes the matrix watch this [video](http://youtube.com/watch?v=vSczTbgc8Rc) 
# Moore Penrose PseudoInverse
# Principal Component Analysis
# References
1. https://www.deeplearningbook.org/contents/linear_algebra.html
2. [Linear algebra from a geometrical perspective](https://www.youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab)
3. [Visualizing matrices as graphs](https://www.math3ma.com/blog/matrices-probability-graphs)
4. [Understanding dot product](https://betterexplained.com/articles/vector-calculus-understanding-the-dot-product/) ^649259
5. [Video on Eigen decomposition](https://www.youtube.com/watch?v=ihUr2LbdYlE
6. [Article on SVD](https://gregorygundersen.com/blog/2018/12/10/svd/)