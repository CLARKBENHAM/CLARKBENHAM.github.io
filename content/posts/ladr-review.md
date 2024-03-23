---
title: "Testing: Lotes on ladr"
date: 2024-03-22T13:46:24-07:00
draft: True
mathjax: true
---

	ladr review blog
		Few big ideas and you just keep applying them
			Algebraic Ideas:
				apply an operator to itself
				keep applying operators multiple times
				use the same vector twice
				u=u-a*v + a*v. For what 'a' does this help you?
				try swapping <u1,v1> to <v1,u1>
				(T-λI) is a null operator if you only look at it acting on the eigenvectors of λ.
				With generalized eigenspaces there's a lot of T^(i-1)T^i counting arguments

			Others
				Represent complex numbers as matrix with a+ib=[[a,b], [-b,a]]
				define the new operator/poly as a tweak of the old operator/poly
				summing everything if that's how I can ‘use’ a constraint
				create a subspace <v,T(V),..> where a constraint isn’t true.

			Read all the theorems in the subchapter, think about which to apply and apply it
				if use the techniques of the proofs in the chapter, was trivial
				When I tried to just do problems via geometry and process for matricies much harder

		The questions are all "easy", but need to check carefully for sublties
			Do every 3rd problem, to make it harder. Eg. normalay problems 8,9,10 are all related and 10 is hard on it's own but easy if you've got the prevous 2 conditions.
			Otherwise if you do every problem sequentially then they're almost all 1 step proofs. There's 1 thing to figure out and then you're done.
		Wish I had harder problems to check this isn't a shallow correlation.

		hardest part is when I mis-remembered something I'd learned previously.
			eg. Min Poly/Char poly can't categorize matrix, since a jordan blocks could be different. eg dim=4, only λ=1. Could have 2 2d jordan blocks or 1 2d block and 2 1d blocks.
			Gaussian elimination changes determinant by product. (Easier way to find determinant)
			SVD sometimes changes which letter "S" represents
			"orthogonal" matricies are composed of orthonormal vectors.

		How to find and extend analogies?
			eg if Normal matricies are Z, what do non-normal matricies corespond to?

		Interesting things learned:
			Solving the Fibonacii sequence was the 2nd coolest part.
			Find the closest approximation of cos using 5 degree polys was coolest
			Every operator can be expressed by polar decomposition, and there's an extension to linear maps.
			Can rotate Every operator so that any vector v is mapped to at most 2D subspace, and these 2D subspaces have at most 2 1-D subspaces overlap.
				Every Normal complex or Self Adjoint (hermitan, symettric if real) real matrix is diagonizable
				Every Complex matrix is Jordan diagonizable
				Ever real matrix has a complexification that is diagonizable, which means can be expressed in blocks of [[a,-b], [b,a]] with b > 0
			Every matrix is diagonizable if you represent it with different input and output basises
			Every matrix in C/with basis of eigenvectors is can be represented as a diagonal matrix, but since eigenvectors aren't orthogonal, it's not 'diagonizable".
				np.diag(eigenvalues) = eigenvectors ^-1 @ A @ eigenvectors

		Missed change of Basis: how to do it better
			The "change of basis" matrix can refer to 2 different things, and notation isn't always concistent. Y=QB or v_y=Qv_b, these Q's are inverses of each other
			image on https://play.google.com/books/reader?id=rRWzNAAAAEAJ&pg=GBS.PA55

		Also doesn't mention
			Gaussian elimination, instead the 'row reduction algorithm'.
				not implied how you can use Gaussian elimination to compute the determinate
			Nor regression: B=(X.T@X)^-1 X.T @ Y
				eg. the geometric perspective of regression as projecting points was useful to me:
					matrix X is N rows/datapoints and P predictors
					matrix Y is N rows, a point
					B is all possible predictors/coefficents
					β is best predictor/coefficents

					In Y's space of dimension N, we want to find the vector in the P dimensional hyperplane that is closest to point Y.

					It's a P-d hyperplane because X@B has P coefficents in B, and those coefficents can take any values.

					The distance from plane X@B to point Y is minimized when the projection of Y onto vector X@β is orthogonal to vector X@β. (That is the error vector vector should go from Y to the tip of X@β)

					Orthogonal when X.T@(Y-X@β)=0 implies β = (X.T@X)^-1@X.T@Y



