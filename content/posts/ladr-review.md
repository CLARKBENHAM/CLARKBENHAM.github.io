---
title: "Notes on Linear Algebra Done Right"
date: 2024-03-25T13:46:24-07:00
draft: True
mathjax: true
---

	ladr review blog

		# Solving the problems in the Book
			Few big ideas and you just keep applying them
				Algebraic Ideas:
					apply an operator to itself
					keep applying operators multiple times
					use the same vector twice
					u=u-a*v + a*v. For what 'a' does this help you?
					try swapping the order:
						for operators ST to TS
						<u1,v1> to <v1,u1>

				Others
					Represent complex numbers as matrix with a+ib=[[a,b], [-b,a]]
					define the new operator/poly as a tweak of the old operator/poly
					summing everything if that's how I can ‘use’ a constraint
					create a subspace <v,T(V),..> where a constraint isn’t true.
					(T-λI) is a null operator if you only look at it acting on the eigenvectors of λ.
					With generalized eigenspaces there's a lot of T^(i-1)T^i counting arguments, understanding how it acts on each direct sum

			The questions are all "easy", but need to check carefully for sublties
				Having a solutions manual to compare to was great: 1. Just "proving" theorems isn't enough. If I've written a proof I assume it's correct, so it's good to catch exactly what I missed.  2. I learned how to write cleaner proof's and with new techniques.
					Solution sources:  https://linearalgebras.com/8b.html, https://github.com/jubnoske08/linear_algebra, https://github.com/celiopassos/linear-algebra-done-right-solutions
					Having good solutions will be a big factor in which textbooks I chose going forward.

				My processes was to Read all the theorems in the subchapter, think about which to apply and apply it
					if use the techniques of the proofs in the chapter, most of the problems are trivial
					When I tried to just do problems via geometry and extend the processes as if I was acting on matricies it was much harder

				Do every 3rd problem, to make it harder. Eg. normallay problems 8,9,10 are all related and 10 is hard on it's own but easy if you've got the prevous 2 conditions.

				Otherwise if you do every problem sequentially then they're almost all 1 step proofs. There's 1 thing to figure out and then you're done.
					eg. k

		hardest part is when I mis-remembered something I'd learned previously.
			eg. Min Poly/Char poly can't categorize matrix, since a jordan blocks could be different. eg dim=4, only λ=1. Could have 2 2d jordan blocks or 1 2d block and 2 1d blocks.
			Gaussian elimination changes determinant by product. (Easier way to find determinant)
			SVD sometimes changes which letter "S" represents across authors.
			"orthogonal" matricies are composed of orthonormal vectors.
			But Overall I wish had harder problems to check I'm not only learning shallow correlations. If I only get stuck when something I memorized conflicts with something else then it shows I'm memorizing surface level attributes and not the structure.


		How to find and extend analogies?
			eg if Normal matricies are Z, what do non-normal matricies corespond to?


		# Change of Basis
			The major oversight of the book was what I thought a lack of focus on change of basis. This is the best way to understand what Product Quantiation[https://www.pinecone.io/learn/series/faiss/product-quantization/] does.

			I'll give a concrete example. Lets say we're a Tailor in a rush whose tried of measuring people and now wants to predict someones suit size from their height and weight. We'll say their suit size is 3 numbers: Jacket Shoulders, Waist, and Length.
			We can think of this regression problem as a linear operator maps between 2 vector spaces [height, weight] to [expected  Shoulder size, expected Waist Size, expected Length ].
				Suit size isn't a linear transformation of height and weight, but the expectation of the size is. Taller people generally have suits with bigger shoulders and lengths and Heavier larger Waists. But there's also a correlation between weight and height.
			When we run the regression we get	T = [[0.25, 0.2], [0.1, 0.1], [0.6, 0.05]]
				This means that for every inch of height, we expect a 0.25 inch increase in shoulder size, a 0.1 inch increase in waist size, and a 0.6 inch increase in length. Similarly, for every pound in weight, we expect a 0.2 inch increase in shoulder size, a 0.1 inch increase in waist size, and a 0.05 inch increase in length.
			And what do you know it works great, and we start making accurate suits far faster than before. We expand overseas. But now we have a problem; other countries use different measurements than we do.
			A person 6ft weighting 200lbs is [1.8m, 90kg], so we can't use the same formula.
				np.array([[0.25, 0.2], [0.1, 0.1], [0.6, 0.05]]) @ [1.8, 90] = [18.45,  9.18,  5.58] a suit only 9 inches at the shoulders.
			This is dumb. Using metric vs imperial units changed what suit customers got, thought the person stayed the same size.

			In Imperial Units we found `T_imperial = np.array([[0.25, 0.2], [0.1, 0.1], [0.6, 0.05]])`. We'll convert from metric to imperial and then run our old regression.
			How do we convert this to imperial? 1 meter is 39 inches and 1 kg is 2.2 lbs. We can express this as an operator `Q_im = np.array([[39, 0], [0, 2.2]])`.
			So when we compose the two operpators: T_metric = T_imperial @ Q_im: T_metric([1.8m, 90kg]) = T_imperial(Q_im([1.8m, 90kg])) = T_imperial([6ft*12in/ft, 200lbs])

			out units in Metric inputs from multiplying the matrix representations of those operators:
				T_metric = [[1.27, 0.0907], [0.254, 0.3629], [1.778, 0.1361]]
				This means that for every centimeter increase in height, we expect a 1.27 cm increase in shoulder size, a 0.254 cm increase in waist size, and a 1.778 cm increase in length. Similarly, for every kilogram increase in weight, we expect a 0.0907 cm increase in shoulder size, a 0.3629 cm increase in waist size, and a 0.1361 cm increase in length.

			Now sales boom so much we're not only selling overseas we move part of our production overseas as well. Now our outputs have to be in terms of metric and not inches.
			So how do we convert to a

			Let Equivalence of T in both units:
			Let T_imperial and T_metric should be equivalent, as they represent the same linear transformation but in different units. We can convert between the two representations using a change of basis matrix.we want but we'll do it in terms of both Imperial and Metric Units.
			Let


			Here's how I'd explain it with concrete representation.
			The "change of basis" matrix can refer to 2 different things, and notation isn't always concistent. Y=QB or v_y=Qv_b, these Q's are inverses of each other
			image on https://play.google.com/books/reader?id=rRWzNAAAAEAJ&pg=GBS.PA55

			What does a Basis mean in the Abstract:
				Not all vector spaces represent numbers along a set of dimensions, nor operators are represent coefficent multiplication on the underlying variables. If your vector space is a set of functions (eg. {1,x,x^2, cos(x), sin(x), e^2x}) then the map T defined by  Differentiation on each element is an operator:
					For T(v) = d/dx v and v=[1,1,1,1,1,1] in the basis above
					Then T([1,1,1,1,1,1])
					=  T([1,x,x^2, cos(x), sin(x), e^2x]) if we change our representation from being in terms of functions to being in terms of e1,e2,e3,e4,e5.
					= T (1 + x + x^2 + cos(x) +  sin(x) + e^2x) what a point in the space actually represents
					= d/dx (1 + x + x^2 + cos(x) +  sin(x) + e^2x) what T actually does on points
					= (0 + 1 + 2x + -sin(x) + cos(x) + 2e^2x)
					= [1, 2x, 0, cos(x), -sin(x), 2e^2x] from the underlying function back to function space
					= [1,2,0,1,-1, 2] the point in function space represented in the original basis
				This is an example of how you can compute the derivative of any elementary function by matrix multiplication; as long as you restrict yourself to a finite collection of elementary functions.
					By Liouvile's Theorem[https://en.wikipedia.org/wiki/Liouville%27s_theorem_(differential_algebra)] Differentiation on an elementary function produces another elementary function. So differentitation is a linear Operator on the space of elementary functions.




		Interesting things learned:
			Reisz Representation Theorem was by far the coolest:
				Every finite dimensional linear function φ: V->f has a vector w such that φ(v) = <v,w>.
				This seems a little obvious if you're only imagining functionals as "rulers" on a specific basis: given a vector you decide how much each component should could and then sum the entries weighted by your functional. eg. thinking of φ in R^3 as φ([1,2,3]) = f(1) + g(2) + h(3) linear functions f,g,h that map 0 to 0 can only be expressed as a*1 + b*2 + c*3. That's clearly a multiplication with another vector in R^3.
			Is this Reisz or orthonormal projection?
				This lets you say that every integral is the product of with another poly!
				eg. Find the closest approximation of cos using 5 degree polys

			Solving the Fibonacii sequence was the 2nd coolest part.
			Every operator can be expressed by polar decomposition, and there's an extension to linear maps.
			Can rotate Every operator so that any vector v is mapped to at most 2D subspace, and these 2D subspaces have at most 2 1-D subspaces overlap.
				Every Normal complex or Self Adjoint (hermitan, symetric if real) real matrix is diagonizable
				Every Complex matrix is Jordan diagonizable
				Ever real matrix has a complexification that is diagonizable, which means can be expressed in blocks of [[a,-b], [b,a]] with b > 0
			Every matrix is diagonizable if you represent it with different input and output basises
			Every matrix in C/with basis of eigenvectors is can be represented as a diagonal matrix, but since eigenvectors aren't orthogonal, it's not 'diagonizable".
				np.diag(eigenvalues) = eigenvectors ^-1 @ A @ eigenvectors

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



