---
title: "Koans on Circuit Breaker Results"
date: 2024-11
draft: False
mathjax: True
---

# Adversarial Probes for NSFW Detection in Language Models: Theory and Implementation

## 1. Introduction & Project Motivation
### 1.1 Context and Goals
- Primary goal: Create models resistant to red-teaming attacks (even with 1000+ dedicated hours)
- Method: Detect specific concepts that LLMs use in a robust way. "Turn off concept" and attack impossible.
- Practical application: Prevent NSFW completions
- Introduction to adversarial probing and soft prompts mechanics

### 1.2 Impact Areas
- Long-term alignment: Understanding and controlling model behavior
- Short-term business value: Deploying safer models
- Overview of Circuit Breakers (CB) approach as comparative baseline

## 2. Technical Approach: Probes
### 2.1 Probe Fundamentals
- Monitors intermediate steps in transformer model processing
- Analyzes internal activations using binary classifier: logistic regression/MLP
- Convert to standard data science problem (CSV with numbers -> binary output)
	- Predicts likelihood of NSFW content in final output
- Default is massive parameter spaces (number of activations), how to aggregate?

### 2.2 Understanding the Parameter Space
- High-dimensional space representation
- "Ball of hair" analogy: thin wires of meaningful paths
- Importance of staying on "meaningful manifolds", e-ball is too random
- Random noise vs. semantically meaningful changes

### 2.3 Probe Training Process
- Store intermediate vectors to disk
- Optimization: Average embeddings across token locations for efficiency
- Probe averaging techniques
- Linear probes (logistic regression) provide computational benefits
- Adversarial training loop: gradient descent attack against all previous probes and predictions to create new attack text with softprompts. Then train new probe against cumulative attack text.
- Loss: attack is bad plus Log entropy of probe predictions

## 3. Technical Implementation

### 3.1 Dataset Selection
- Need for meaningful harmful vs. non-harmful distinctions. Model must use it's own intelligence to cause harm, not repeat back what user said.
- Dataset quality: should be toughest epsilon % of real distribution.
- Random ChatGPT queries from HarmBench and WildChat
- Aside: test set duplication and mis-labeling is acceptable. Multiple completions for similar prompts or incorrect labels maintained for comparison with other methods. We don't care about absolute performance only relative difference across other authors or our ablations. Real life long tail not as applicable if have hard test set.

### 3.2 Loss Measurement Design
- Goal: Accurately identify if output bad, have gradient, be fast
- Used KL divergence between original and new predictions at each index of completed prompt.
	-	Single forward pass no generation to evaluate. input=[Softprompt, Original Text Prompt, Models original generation], loss=KL(original_generation_tokens, new_generation_log_probs)
- Limitations of alternative loss functions (see section 4):
  - Why simple classification losses fail (incorrect or attacker just fools grader)
  - Challenges with direct output comparison (full generation vs 1 pass slower; again have to align on rules)
  - Need for balanced detection vs false positives

### 3.3 Attack Space Definition
- Use of soft prompts instead of discrete tokens
- Rationale:
  - Better gradient information
  - More manageable optimization space
  - Linear combinations of token embeddings
  - Assumed equivalence between token prefix sequences and soft prompt prefixes
		- There's some 1k token prefix sequence that has numeric impact similar to the 5 token softprompt selected
- Implementation details:
  - Limited to 5 insertion locations
  - Embedding size considerations
  - Balance between effectiveness and realistic attacks

## 4. Content Evaluation Approaches
### 4.1 Direct Output Matching (Next Token Prediction)
- Simply check if model outputs same problematic content
- Advantages:
	- Single forward pass vs. full generation
  - No need for autoregressive generation
  - Fastest approach (~100x speedup for 100-token generation and long prompt)
  - Avoids many edge cases

### 4.2 Full Text Evaluation
#### 4.2.1 Regex Approaches
- Simplest implementation
- Major limitations:
  - Fails on context-dependent cases
  - Can be easily fooled (e.g., caesar cipher)
  - Misses business logic exceptions

#### 4.2.2 Basic Classification Models (BERT/OpenAI Contend Mod API)
- Limitations:
  - Still relatively simple to fool
  - Misses contextual nuances
  - Better than regex but inadequate for edge cases

#### 4.2.3 Advanced Model Classification
- Using original model or sophisticated classifiers
- Challenges:
  - Rule alignment issues between providers
  - Fundamental ambiguity in content moderation
  - Expert disagreement (e.g., Supreme Court on defining pornography)
  - Cost implications of large model forward pass plus gradients
		- effectively training 2 models simultaneously if have gradients flow through

## 5. Performance Analysis
### 5.1 Circuit Breakers Plan
- Hypothesis of where CB results from:
	- Weak test-set
	- Dataset has spuriously correlated feature
	- Better hyper-parameters/Extra compute for searching
	- Better features for probe
	- New loss function trains good classifier
	- Didn't investigate:
		- Destroying activations easier than classifying
		- Just trained a full model and probe simultaneously
		- Forward pass: Passing information from earlier layers to help make later predictions
		- Forward pass: Passing information from earlier layers, plus the processing model does on that information at intermediate step, to help make later predictions
		- Generation Dynamics: Passing information from earlier completions to later
- Testing Plan:
	- Confirm Data pipeline
		- That Individual examples hard:
			- OpenAI moderation API
			- OpenAI text model label "bad" or "good"
			- Finetune bert to predict:
				- Replace content with empty string, how much info is in just the prompt?
				- if example came from the training or test distribution
		- That test set isn't correlated (eg. all bad cases in german )
			- Finetune Bert to solve task
			- Finetune OAI text model to label "bad" or "good"
				- OAI doesn't let you upload csv of bad text even with obfuscation anymore[https://clarkbenham.github.io/posts/red-team-summary/]
	- Not from Extra Compute
		- LoRA train with full LoRA parameter sweep
			- LoRA parameters that matter: r, lora_alpha, lora_dropout
			- batch size, learning rate. Not LR scheduler, weight decay. Only try 1 long run to see how many epochs need for good result and then use that fixed number of epochs.
		- Finetune full model
	- Not from Better Features:
		- Train probes on CB model (was partially true)
		- Train probes on CB and see how generalize to base model (generalize better)
		- Manual Feature engineering:
			-	adding norm of activations and binary variables for is greater than xth percentile
		- Use larger model for probes

### 5.2 Circuit Breakers Comparison Results
- https://docs.google.com/spreadsheets/d/1KuAl6b1zKRHjytQdW4Es9u1pzZ3VGqcKlxnwPvSxpb4/edit?gid=2023353007#gid=2023353007
	- Plots to match table 2 of Circuit Breaker Paper.
	- Only test on the NSFW cases the model original answered, but adjust the change in rates by (1-FN) to get original numbers
- LoRA-trained classifier achieved similar performance
- Probes can be effectively stacked with Circuit Breakers
- Standard probes with features slightly underperformed
- Layer selection and token index aggregation impact

### 5.3 Generalization Capabilities
- Cross-jailbreak generalization showed promise
- Transfer between base and chat versions, CB and regular models even better.
- Zero-shot generalization to held out jailbreak classes. But in production would train on all JBs classes had. This catches more JB classes but misses more examples within each classes.

## 6. Development Best Practices & Lessons Learned
### 6.1 Technical Challenges
- Too many unit tests, not ideal for ML workflow
- Challenges with Ray: editing files Ray imports in distributed runs will the change file on next run: Ray is in separate processes.
- Installing Nvidia drivers: also need fabric for Multi-GPU

### 6.2 Methodology Recommendations
- Focus on specific questions over feature implementation
- Prioritize direct accuracy metrics over visualization
- Small Tests: single dimension at a time not hypercube
- Don't care about absolute performance, only relative to otherwise identical system. So don't worry about data quality as long as it is what they do.
- Standardized workflows (e.g., weights & biases, bash script starting python code)
	- forces separation of config code and implementation code
	- easy review of results
	- include hash of config file
- Accept negative results earlier: "5 days work closed 7% of the gap so current approach insufficient"
- Write up results after each day

## 7. Future Directions
### 7.1 Attack
- Try with more realistic attacks:
	- GCG/AGC
	- Full live dataset of attack prompts

### 7.2 Dataset Considerations
- Creating more representative harmful content datasets
- Better harm classification methods
- Cross-domain generalization strategies

### 7.3 Scalability Challenges
- Save activations in format that loads least extra information into memory for aggregation
- easier if 1 model for 1 GPU

## 8. Key Lessons
- Importance of semantic meaning in transformations
- Challenge of defining and detecting "bad" content
- Need for efficient, reliable detection methods
- Fastest steps to answer key uncertainty.