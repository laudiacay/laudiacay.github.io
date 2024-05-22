---
tags:
  - ilya30u30
  - ai
  - writing
  - papers
title: Tutorial Introduction to MDL
collection: posts
layout: post
---
### [Source post here.](https://arxiv.org/pdf/math/0406077)

## UNDER CONSTRUCTION! as of may 13 2024. please excuse the awful formatting, I'm working in markdown and not compiling to HTML

What a great start, this is eighty pages long. It's remarkably friendly- if you're using my notes to skip the papers, maybe just go read this one and refer back to my notes when you're having trouble parsing something. (or want some fun Claudia insights about applications to broader ML/my one true love cryptography)
#### Background
The start of the paper: the MDL (Minimum Description Length) is a method for inductive inference. What's inductive inference, you ask? It's the task of finding a general model from a finite set of training data. You want to fit the observed data well, but also generalize to data from the same distribution that you haven't seen before. 

If you've heard the terms "underfitting" and "overfitting", those are describing not fitting the observed data well enough (e.g. not learning enough about it to predict future observations well), and fitting the observed data too well (e.g. seeing patterns that aren't there and messing up accordingly on future observations).

MDL more or less says that any pattern you find in the data can be used to compress the data. The more you've learned about the data, the more you're able to compress it, and vice versa. You want to minimize both the length of the description of your model, and the length of the observations given the description of the model, and then you'll be able to predict the future (sort of).

MDL has some extremely nice properties:
- it looks like Occam's razor, 
- it naturally protects against overfitting, unlike maximum likelihood estimators
- It has a Bayesian-ish vibe, but avoids the weird interpretation issues when we know there's not REALLY a ground-truth distribution to learn
- it doesn't make any assumptions about whether there is some "underlying truth" (great, nobody who studies language today is a hardcore Platonist)
- data compression and prediction are formally equivalent for a certain definition of prediction, MDL is leveraging this in order to predict
### Kolmogorov Complexity for Compression

Let's start: imagine some sequences of bits, like "101010" (a pattern) and "111011111011110101" (far more ones than zeros but otherwise random) and a completely random string. The ones with regularities you can spot are compressible with some O-notation bound on how compressible it is. 

The next step is how we should go about compressing these strings, and we go with Kolmogorov Complexity. If you can write a computer program that is able to write the sequence out, and the computer program is shorter than the length of the sequence, you have successfully compressed it. Kolmogorov Complexity is defined as the length of the shortest computer program that prints out the sequence in question. This sounds really arbitrary and up to choice of programming language, but it turns out [there's a proof](https://dl.acm.org/doi/pdf/10.1145/321495.321506) that asymptotically all programming languages are only a constant factor apart. 

Backing up from this definition of Kolmogorov Complexity, you can get an "idealized MDL" for an "ultimate model of the data", which is exactly just this shortest computer program... Unfortunately, this is both practically uncomputable (there's a proof saying as much), and is dependent on the syntax of the programming language you choose. So, in reality, you take "practical" approaches to finding an MDL predictor, by using description methods that know things about the specific problem domain, generally doing the best you can in the situation.
### Practical MDL, starting with "Crude MDL"

Grounding: We start with the simple example of picking a good polynomial estimation from a set of points without over or under fitting. Then we define some terms: "hypothesis" as a single probability distribution (in this case, a single polynomial), and "model" is a family of probability distributions with the same form (second-degree polynomials). Note how this maps onto our intuition from machine learning: a model is how you wire up the layers in Tensorflow and set up training, a hypothesis is a frozen-in-time partly-or-completely-trained model that you might test on a holdout set.

Next- we've finally arrived at some math! A crude definition of MDL. We define the "best model" in a simplified setting where we have a list of candidate models ($H_1$, $H_2$, $H_3$) containing hypotheses... imagine each one is "the set of all polynomials of degree N". And we have a bunch of data we're trying to explain, $D$.
1. $L(H)$ is the length in bits of the description of the hypothesis
2. $L(D|H)$, length of data given $H$, is the length in bits of the description of the data when you use the hypothesis to explain the data. This will be minimized with a well-chosen $H$, so you only have to encode discrepancies between $D$ and $H$ as an error term. More about this in a second
3. The best model is the one that minimizes $L(H) + L(D|H)$- fitting $D$ well without packing so much information into $H$ that it explodes in size

Now let's make things more concrete. We'll start with $L(D|H)$: Assume $Y=H(X) + Z$, where $Z$ is a noise term. To encode these errors, they're using something called the Shannon-Fano code, which is not defined concretely but is proven to exist for all data sequences, and has length $L(D|H) = -\log P(D|H)$. This looks sort of like Huffman coding to me, but I think it's maybe not actually defined over all probability distributions over the rational polynomials. But claim there's a proof of existence in section 2.2, so I can't wait to check it out.

Defining $L(H)$ gets a bit harder: How do you code hypotheses? Your choice of code affects the outcome of the procedure, because the same hypothesis can vary wildly in length based on code choice. We need to refine MDL a bit to make this make sense...

### Refined MDL
The first thing is to smush the encoding into one part: you encode $D$ with respect to the entire model, instead of given one hypothesis. You design the code so that when there's a member of the model class that fits the data well ($L(D|H)$ small), you get that $\bar{L}(D|\text{model})$ is also small. This $\bar{L}(D|\text{model})$ is called the "stochastic complexity" of the data, given the model.

Next, we add something called the "parametric complexity" of the model, denoted $\textbf{COMP}(\text{model})$, which is a measure of how rich the model is, e.g. the "geometrical structure and degrees of freedom", which also indicates how well it fits random data.

There's a relation between parametric complexity and stochastic complexity- let $\hat{H}$ be the distribution in the model that maximizes the probability of $D$ and therefore minimizes the complexity of the hypothesis.
$$\bar{L}(D|\text{model})= L(D|\hat{H}) + \textbf{COMP}(\text{model})$$
Note that this is the same as one of the early attempts at calculating "crude MDL" mentioned in the paper, where the researchers were choosing hypothesis codings that would minimize this term... Except this one cleverly elides the hypothesis coding problem entirely, so there's no arbitrariness, and you get something concrete that you can compute.

The paper goes on to list four interpretations of this definition:

1. Counting states: parametric complexity of a model is the logarithm of distinguishable hypotheses within it
2. Two part coding: we're reducing back to the crude MDL definition, e.g., stochastic complexity is a two part code's length, where you break the model into "maximally distinguishable hypotheses" and then just assign them all the same codelength ($\textbf{COMP}(\text{model})$)
3. Bayesian: a non-informative prior aims to minimize the bias introduced by prior assumptions onto the bayesian inference procedure as you learn over data. This procedure is more or less doing the same thing- it's picking the model based on minimizing the code length of a best-fit hypothesis for random data.
4. "prequential interpretation": you're selecting the model with the best performance on unseen test data. This means that you're encoding everything you know about the problem and nothing else, and leaving the model training to handle your hypothesis test: e.g., optimal breakdown of the problem!
### Rissanen's philosophy about MDL

Rissanen invented MDL. 

His thoughts about it: you don't want to assume the observed data was generated by a distribution and try to fit. Instead, you want to start with almost nothing assumed about the structure of the data, and you just want to wring as much "regularity" out of the data as possible in order to learn about it (and therefore compress it). 

Second, he interprets models as languages for describing the useful properties of data, and hypotheses are to be interpreted as metrics and statistics about the data seen, that summarize certain regularities. They're meaningful regardless of whether the hypothesis is the "state of nature", which honestly... if you look at human language... would be a pretty silly thing to talk about. Additionally, noise is not defined relative to some theoretical probability distribution, but as relative to the model once you've found the hypothesis and observed the data- e.g., it's the remainder after you've pulled out as much regularity as you can using the current model! It's just a measure of how good the model can suit the data in question.

Third: do not use methods of inductive inference that assume there's a "true state of nature" you're attempting to approximate. These methods (Markov models for language generation, anyone?) have incorrect assumptions that will bound their "fit" to reality. They can still be useful, but they're not learning from the data alone, they're learning from the data while hobbled by incorrect assumptions.

Fourth: 
