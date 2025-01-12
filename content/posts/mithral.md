
---
title: "[koans] Bolt: Usefully Faster Matrix Mults by Approximation"
date: 2024-03-25T13:46:24-07:00
draft: False
mathjax: true
---

This [project](https://github.com/CLARKBENHAM/bolt) used the [Bolt algo](https://arxiv.org/abs/2106.10860), fixed the C++ implementation, and wrote Python bindings to be usefully faster than the default numpy matrix multiplications. It can be 10-20x faster with 0.1-2% classification errors.

The compressed text outline is LLM expandable.

	How Mithral Works
        Data access pattern: https://github.com/dblalock/bolt/blob/master/assets/blalock-maddness-poster.png

		Optimized Product Quantization but:
			Bolt Increases the number of subspaces: more expressive with the same runtime

			Smaller LUTs so fit in SIMD not L1
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
		Where the previous classification output was correct, is it still correct? Argmax not L2-norm
			asking: does it preserve the argmax of output vector, but biased since correct answers more likely to have large seperation.
			There is Moderate correlation of error with absolute seperation
			For a general matrix multiplication you're concerned with the magnitude of all outputs remaining similar.
				Eckart–Young–Mirsky shows that no rank-k reconstruction minimizies any Unitarily Invariant norm better than SVD.
                    The Frobenious norm is a unitary transformation of the spectral norm.
                But here we minimize the number of bits for each entry, not the rank as a whole.
                If the input only ever had`2**(num hash steps)` distinct numbers in each subspace BOLT would be a perfect approximation: they would be computed beforehand, and the hash function would learn to return them.
                
				And classification is not a unitary norm. 
                    Here we're trying not to change the eigen vector associated with the old maximum Singular Value.
					    We can change the spectral norm, eg. outputing [2,1,1] instead of [4,2,2] would still be correct for classification.
					    But we could keep the spectral norm and be incorrect, outputing [2,2,4]
                    A norm can't measure "distance" from a non-zerovector; by definition ||x||=0 iff x=0

		Works best when: For matricies X (N,D) and Q (D,m) Mithral is optimized for  N > D,M. It also works better when X changes and Q is fixed

		By R^2 for random matricies, does okay. But great on data matching expected distribution
			Pre-quantized data can be computed exactly: eg. all entries are ints in [0,16]

	Existing bugs in the code
		Python version tested for correctness, C++ version tested for speed but implementations not the same.
			speed test with and without changes showed only minor variation, bugs did not cause speedup


	My Multi-monitor setup and things learned
		debugging in docker: debugging requires system calls which aren't enabled by default in Docker. You need to run the container with: `docker run -it --rm --cap-add=SYS_PTRACE --security-opt seccomp=unconfined`
            Build the C++ code in debug mode, set breakpoints in C++ vscode. For Python allow the debugger to step outside of your code. Run python, stop it at a breakpoint, attach gdb to the python process and then continue, the debugger can now step across languages.
            See (.vscode/tasks.json)[https://github.com/CLARKBENHAM/bolt/blob/cb12d40af18eb47c3012af6d4fb5539e0408df8c/.vscode/launch.json#L11] to have this done without manual intervection, including re-building C++ code.
		vscode mutli-window: use a single workspace config file but define it as multi-root.
		installing built in docker: just use Nvidia's provided Dockerfiles and don't manully find compatable versions
		bazel builds: set keywords to define which function to use for build. Can write a simple implementation and a complex implementation and via 1 word hotkey swap between.
            Also bind build task to hotkey: 
		pybind11: would rather transfer pointers than data, so must specify rules to change owner from C++ to Python. Serialization for various types is also not automatic due to format conversion. (Code)[https://github.com/CLARKBENHAM/bolt/blob/cb12d40af18eb47c3012af6d4fb5539e0408df8c/cpp/mithral_wrapped.cpp#L337] to return results and accept inital parameters.
