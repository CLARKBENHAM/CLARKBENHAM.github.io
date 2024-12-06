
---
title: "[koans] Mithral: Usefully Faster Matrix Mults by Approximation?"
date: 2024-03-25T13:46:24-07:00
draft: False
mathjax: true
---

The compressed text outline is LLM expandable.

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
			TODO: check correlation of error with absolute seperation
			For a general matrix multiplication you're concerned with the magnitude of all outputs remaining similar.
				But Mirsky showed that SVD minimizes any reconstruction Norm that's the result of a Unitary Transform.
				The Frobenious norm is a unitary transformation of the spectral norm.
				Here we're trying not to change the vector associated with the old maximum Singular Value.
					We can change the spectral norm, eg. outputing [2,1,1] instead of [4,2,2] would still be correct for classification.
					But we could keep the spectral norm and be incorrect, outputing [2,2,4]
				So this is problem unitarily equivalent to the frobenious norm?

		Works best when: For matricies X (N,D) and Q (D,m) Mithral is optimized for  N > D,M. It also works better when X changes and Q is fixed

		By R^2 for random matricies, does okay. But great on data matching expected distribution
			Pre-quantized data can be computed exactly: eg. all entries are ints in [0,16]

	Existing bugs in the code
		Python version tested for correctness, C++ version tested for speed but implementations not the same.
			speed test with and without changes showed only minor variation, bugs did not cause speedup
		link to gitissues?


	My Multi-monitor setup and things learned
		debugging in docker
		vscode mutli-window
		installing docker (just use Nvidia's provided Dockerfiles)
		bazel builds
		how pybind11 transfers data
