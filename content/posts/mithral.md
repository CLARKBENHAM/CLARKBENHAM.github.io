
---
title: "Mithral: Usefully Faster Matrix Mults by Approximation?"
date: 2024-03-25T13:46:24-07:00
draft: True
mathjax: true
---

	How Mithral Works
		Insert diagram of data access

		OPQ but with:
			Bolt Increases the number of subspaces: more expressive with the same runtime

			smaller LUTs so fit in SIMD not L1
				linearly approximate and rescale to get correct answer
			They quickly sum 8-bit ints by averaging pairs of values in a tree structure. (if you summed the ints and then divided you'd have to cast to 16 bit first)

			Fast Hashing function to pickout centroid ix
				Why is the Mithral  hash function easy to vectorize on rows of X if X is stored in column major order?
					the hash fn uses the same 4 indicies at every level; In col major order 4 X columns are all the info you need so pull them out and run SSE on each array

			OLS to optimize the centroids for each subspace to minimize the reconstructed error of the training matrix
				How does Mithral use Regression to optimize multiple prototypes per subspace at once? You have nrows > ncentroids; A=G@P where G selects the prototype of each subspace for each row of A, Optimize P. OPQ but only done once, since you hash vectors to the 'same' prototypes unless you recompute the hash fn.
				Doesn't have orthogonal subspaces
					Because to find the closest centroid you still only have to hash the values INSIDE the subspace. If you were using L2 you'd have to do multiplications over any centroid's non-zero dimension.


	What the paper shows
		Only on last layer of Model
		Where the previous classification output was correct, is it still correct?
			asking: does it preserve the argmax of output vector, but biased since correct answers more likely to have large seperation.
			For a general matrix multiplication you're concerned with the magnitude of all outputs remaining similar.
			TODO: check correlation of error with absolute seperation
		Only on test set? (Double check accuracy numbers)

		Works best when: For matricies X (N,D) and Q (D,m) Mithral is optimized for  N > D,M. It also works better when X changes and Q is fixed

		By R^2 for random matricies, does okay. But great on data matching expected distribution

	Existing bugs in the code
		link to gitissues?
		TODO: speed test with and without changes

	My Multi-monitor setup and things learned
		debugging in docker
		vscode mutli-window
		installing docker (just use Nvidia's provided Dockerfiles)
		bazel builds
		how pybind11 transfers data
