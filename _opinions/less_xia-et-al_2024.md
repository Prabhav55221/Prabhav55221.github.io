---
layout: opinions
title: "Gradient Clustering = Entropy based Active Learning?"
date: 2025-05-26
topic: "NLP"
paper_title: "LESS: Selecting Influential Data for Targeted Instruction Tuning"
paper_authors: "Xia et al."
paper_url: "https://arxiv.org/abs/2402.04333"
excerpt: "Pretty cool to see the selection dynamics when the proposed method is used for Active Learning."
---

## Introduction to the Paper

Now this won't be an overview of the paper in itself - I recommend reading the amazing [paper](https://arxiv.org/abs/2402.04333) anyway. I will focus more on the idea used to drive the instruction tuning approach presented in the paper and then delve into its applications for Active Learning and what I observe from those experiments. 

The paper in general presents LESS (Low-rank gradiEnt Similarity Search) an optimizer-aware and practically efficient al gorithm to estimate data influences and perform for instruction data selection. In other words, let's say you have to choose data in a principled manned for instruction tuning. What are the general methods for this? Well you could:

- Use all the data - You would probably reduce your taks loss pretty well but this might be too costly in general.
- Randomly select the data with constraints on amount of data you can select - Would reduce your cost but the everpresent baseline is random selection is probably not effective on task loss.
- Use entropy as measure [[1]](https://arxiv.org/abs/2204.07965)? High entropy of the model on certain datapoints probably shows that the model would benefit if its parameters are tweaked to reduce loss on those datapoints. However, this doesn't perfectly represent model uncertainty and can lead to issues like overfitting on noisy data or prioritizing *hard* samples near decision boundaries that might not generalize well. Additionally, models can be bad at estimating their own uncertainty, making entropy-based methods less effective in those contexts [[2]](https://arxiv.org/abs/2310.19573). 

This paper proposes something different (based on some prior work [[3]](https://arxiv.org/abs/2002.08484)) - prioritizing training on data that directly minimizes loss on a target task - which in this case is instruction tuning of some LLM. See the figure below for some idea on the method.

<div style="display: flex; flex-wrap: wrap; gap: 20px; margin-top: 10px;">
  <img src="https://Prabhav55221.github.io/images/opinion_images/less_main_figure.png" alt="LESS Main Figure" style="width: 100%">
</div>

## Some Concepts

Time for some maths. The idea, taken from [[3]](https://arxiv.org/abs/2002.08484), is to use first order approximation of training dynamics to estimate influence of a datapoint on the model parameters. Consider a model paramterized by $\theta$ at time step $t$ trained on the loss $\ell(·; \theta_t)$. We can write the first-order Taylor expansion of the loss on a validation datapoint $z'$ as

$$ \ell (z' ; \theta_{t+1}) \approx \ell(z' ; \theta_t) + \langle \nabla \ell (z' \theta_t) ; \theta_{t+1} - \theta_t \rangle $$

Continuing, if we run **SGD with batch-size 1** and learning-rate $\eta_t$

$$
\theta_{t+1}\;=\;\theta_t\;-\;\eta_t\,\nabla\ell(z;\theta_t),
$$

then plugging the update into the Taylor expansion gives the **per-step influence** of training point $z$ on the validation point $z'$:

$$
\boxed{\;
\Delta\ell_{t}(z,z')\;=\;
-\,\eta_t\,\bigl\langle
      \nabla\ell(z';\theta_t),\;
      \nabla\ell(z;\theta_t)
\bigr\rangle
\;}
$$

> **BIG IDEA!** A *positive* inner product (aligned gradients) means the step lowers the loss on $z'$; a negative one means it hurts.

Aggregating these contributions over all epochs in which $z$ appears yields its **trajectory influence**

$$
\boxed{\;
\text{Inf}(z,z') \;=\;
\sum_{i=1}^{N}\eta_i\,
\bigl\langle
      \nabla\ell(z';\theta_i),\;
      \nabla\ell(z;\theta_i)
\bigr\rangle
\;}
$$

This scalar score tells us how helpful (or harmful) $z$ is for improving performance on $z'$. LESS simply picks the training examples with the *largest* positive influence, giving a tiny yet highly effective instruction-tuning set.

See Section 3 in the paper to understand a bit more on how this basic idea extends to the Adam optimizer - The idea is that the parameter updated becomes a bit more complex but you can still derive the influence of a data point in the same manner.

> Interesting Point - SGD actually hurt the performance of instruction tuning when used with this concept. The idea works with Adam however. Maybe this could be attributed to the general fact that Adam seems to works better than SGD for LLMs (don't quote me).

## Applications to Active Learning

Now the same idea is very applicable to active learning - which isn't explored in the paper directly since the focus is strongly on instruction tuning. One very obvois way in which it could be used in active learning is represented in the BADGE paper [[4]](https://arxiv.org/abs/1906.03671). The paper method states: `Batch Active learning by Diverse Gradient Embeddings (BADGE), samples
groups of points that are disparate and high magnitude when represented in a hallucinated
gradient space, a strategy designed to incorporate both predictive uncertainty and sample
diversity into every selected batch.` The idea is simple:

1. **Hallucinate labels**: For each unlabeled point, predict what the model *would* label it as (its most likely class).
2. **Compute gradient embeddings**: Treat this prediction as true, and compute the gradient of the loss with respect to the model’s final layer — this gives a *pseudo* update direction if we were to train on this point.
3. **Cluster in gradient space**: Use *k-means++* to select a diverse subset of points in this gradient space — prioritizing samples that would lead to different parameter updates.
4. **Query and train**: Label those diverse points, add them to the labeled set, and retrain the model.

This approach balances *how uncertain* the model is about a point with *how different* it is from others — making every annotation count.

#### My Implementation and Observations

I implemented Algorithm 1 defined in the paper with one key difference:

> Start with a model with **no knowledge of the task** - a *cold start*! Can the algorithm balance diversity and uncertainity in the same way?

Further, I compare this method with something we are working in AnnotationArena (ongoing project with my advisor Prof. Eisner and collaborator Haojun Shi at CLSP) - Idea is still same but instead of clustering gradient, can we align the gradients of the active pool with the gradients with respect to the parameters of the model at each cycle. We do this by computing the same inner product but sorting topologically by the magnitude of gradient and picking top K samples.

Results? Diversity is not that big of a factor when it comes to actually reducing a task loss - In our case the task is to reduce loss on a certain target variable (I won't go into the details, but the variables are ratings of LLM generated stories by other LLMs and Humans). Turns out at a more granular levels, these gradients can be clustered in a more principled manner. Here are the plots:

<div style="display: flex; flex-wrap: wrap; gap: 20px; margin-top: 10px;">
  <img src="https://Prabhav55221.github.io/images/opinion_images/badge_comparision.png" alt="Morning run at JHU" style="width: 50%">
  <img src="https://Prabhav55221.github.io/images/opinion_images/diversity_score.png" alt="Night run at JHU" style="width: 50%">
</div>

We can also see the selection process with T-SNE:

![Alt Text](https://Prabhav55221.github.io/images/opinion_images/gif_vis.gif)

## Conclusions

`Still under work!`