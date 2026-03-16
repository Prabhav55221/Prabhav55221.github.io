---
layout: reading-note
title: "Rewarding Progress: Scaling Automated Process Verifiers for LLM Reasoning"
date: 2026-03-16
week_of: "March 9, 2026"
papers: "Setlur, Nagpal, Fisch, Geng, Eisenstein, Agarwal, Agarwal, Berant & Kumar — ICLR 2025"
excerpt: "Notes and collaborative research directions on PAVs — process advantage verifiers that measure reasoning progress as step-level advantages under a complementary prover policy — covering the fixed prover problem, adaptive prover curriculum, and an extension of the three-signal RLVR objective to reasoning traces via pragmatic probes."
---

## Basic Overview

In general, I really liked this paper. I thought it's very well written! The writing is crisp and human-like (for once) and very simple. All ideas are presented very cleanly without the usual mathematical jargon in RL papers. The paper basically focuses on a discussion on **PRMs for LLM Reasoning**. They pose two very simple questions:

1. If we are to use PRMs, how do we design these **process rewards**? In a bit more detail, do we decide these process rewards by comparing reasoning against human annotated chains or other cheaper options like automatically labeled datasets? They propose that both are unnecessary. They propose instead that the process reward can simply be measured as a **change in likelihood of producing a correct response in the future** (not at the moment — this is important!).

2. If we are to do this, which **prover** do we use (a prover is just the policy model that, as far as I understand, acts as the oracle to compute likelihood against)? They propose that it's best to use an "okay prover": something not too strong (an oracle model would just find the answer from any reasoning trace) and also something not too weak (using the base model itself would give an advantage of zero).

Their results are super cool. In general, I love the idea of using toy examples like the **didactic setup** to show their theory in action. Now obviously I think their actual final experiments leave a bit to be desired — the amazing presentation of the theory and toy examples makes up for it. I would like to see more results on different reasoning benchmarks and model families with different baselines to define the notion of progress reward (maybe the appendix has it? I should read that.).

---

## Some Details I Thought Were Important

Ok so here are points I noted while reading the paper. Some are just observations which are for my benefit. Some are things I loved a lot and math that I thought popped out very cleanly. Very rarely (at least for this paper), there are also critiques.

**1.** Is this something that can be used as justification for doing more **online RL**? If a base model itself can serve as the prover with the right modifications to the approach, why don't people try online RL more? It's probably better to do it in an online manner as is made evident by the improvements when doing beam search with the same approach (I may be wrong with this logic).

**2.** Can easily equate this to a **student-teacher setting** (in real life and in the more common LLM literature). A strong teacher is sometimes too good and a weak base student (or policy) may prefer having a better teacher. A better teacher is probably one who learned in the same way — started off weak with "bad traces" but with a good sense of which traces can be good. I like the analogy. Maybe should formalize it more.

**3.** A lot of talk about **explore vs. exploit** here. People in RL talk about the balance between these two, but they seem to strongly favor explore here. Most of their approach is based on the idea that if the base model comes up with any trace possible, it's ok to assume its validity and instead of punishing it, reward it in the "right direction". Is explore then the right way to do RL or distillation?

**4.** Clean maths in the introduction. The key insight is that if the process reward at any step is set to the **base policy's own advantage** $r_h = A^{\pi}(s_h, a_h)$, then by the Bellman decomposition $A^{\pi}(s_h, a_h) = R_{\text{ex}}(s_h, a_h) + V^{\pi}(s_{h+1}) - V^{\pi}(s_h)$, the sum of advantages over a trajectory telescopes:

$$\sum_{h=1}^{H} A^{\pi}(s_h, a_h) = \sum_{h=1}^{H} R_{\text{ex}}(s_h, a_h) - V^{\pi}(s_1)$$

The $V^{\pi}$ terms cancel to leave only the cumulative extrinsic reward minus a constant baseline — precisely what ORM-RL already computes. So using the base model's own advantages as process rewards adds no new signal. We need the prover to come from outside.

**5.** Are **ORMs** a generalization of the normal RLVR we do? I assume the difference here is that we don't need a verifier. Instead you have a learned model to predict the reward based only on the final output. From the paper, I understand that this is normally done by using a **Best-of-K policy**: where the ORM gives the scores for $K$ samples from the base model. We use the highest!

**6.** **Process Reward Models (PRMs)** are cool. A new concept for me. In short, they evaluate a prefix by computing the expected future accuracy of multiple completions sampled from the base model, after conditioning on the prefix. Here is the math:

$$Q^{\pi}\!\left(\underbrace{(x, a_1, \ldots, a_{h-1})}_{\text{state } s_h},\; \underbrace{a_h}_{\text{action}}\right) = \mathbb{E}_{a_{h+1},\ldots,a_H \sim \pi(\cdot \mid s_h, a_h)}\!\left[\underbrace{R_{\text{ex}}\big((a_1,\ldots,a_H),\, y^*\big)}_{\text{likelihood of future success}}\right]$$

**7.** Is their approach similar to **VoI**? It might actually be. The only catch is how to decide what to compute VoI against. Should think more about this.

**8.** **Major Point:** Since advantages can attach either positive or negative values to a step, the base policy is trained not only when it generates a step that makes progress, but also when it fails to produce one, employing a **"negative gradient"** that speeds up RL training. Read about this (interesting).

**9.** **Major Point (Method):** Change the basic RL objective by an **additive term** (weighted by some constant $\alpha$) which is the expectation over all steps of having some advantage compared to the prover policy — effective reward when scored against ORM + PRM:

$$Q^{\pi}(s_h, a_h) + \alpha \cdot A^{u}(s_h, a_h)$$

where $u$ is the prover policy.

**10.** A weak prover can also amplify a stronger base policy, since a weak prover $u$ may have a lower average of $Q^u$ under its own measure, but still have higher variance across $Q^u$ (compared to $Q^{\pi}$) when evaluated under $\pi$. A lot to unpack here. Nice remark!

**11.** Implementation is pretty simple: use base model as prover but sample a lot of responses. Use BoK over base policy as the prover — $K=4$ works well, but the idea is you want enough variance but not too much variance (should check this).

---

## Ideas That This Paper Gave Me

**1.** I keep thinking of the prover here. Why is the prover required to be the base model? Can PRMs be extended to improving **other aspects of an LLM** by using a simpler prover? In general, the whole idea of a prover is that it's meant to provide some signal to incorporate certain behavior in LLMs. What if we use provers that target specific things in the LLM — reducing **hallucination** for example? Could also be to not exploit loopholes, use maths in a "correct" way, ensure **entailment** of reasoning steps (this one might be particularly interesting to think about).

But wait — the normal RLVR objective for tasks like maths or other logical questions has only verifiable rewards. From our earlier reading (see entries for 2026-03-04 and 2026-03-09), we thought of a three-signal objective:

$$r_{\text{correct}} + \lambda(\hat{a}, \hat{c}) \cdot r_{\text{clarify}} - \mu(\hat{a}, \hat{c}) \cdot r_{\text{loophole}}$$

Previously, this was applied to the final answer. But what if we applied the same idea to **reasoning traces** — using probes trained on the base model to detect when intermediate reasoning steps show hallucination, loophole exploitation, scalar implicature violations, or entailment failures? We could design rewards or punishments to discourage such behavior and point the LLM toward better samples from its state space. PRMs work step-by-step, so we'd be penalizing the model at the exact step where the problematic behavior emerges, not retroactively at the end. Maybe I should think about this more.

**2.** Connections with **VoI** might be interesting here. A good prover policy is one that provides a meaningful signal of information gain at each reasoning step — not necessarily toward a correct answer in the binary sense, but toward reducing expected task loss. Can we formulate the prover selection problem such that the prover is chosen to maximize the expected reduction in uncertainty over the answer space at each step? This would be a different object from expected future correctness: a step could have high VoI even when it is "wrong" in the sense of not appearing in a correct trace, provided it at least narrows down the set of possible answers. How to operationalize this within the PAV framework is unclear to me — but the connection feels worth formalizing.

---

## Collaborative Thoughts

*The following section was developed jointly through discussion. Two directions emerged: first, an extension of the three-signal RLVR objective to process rewards using probes for pragmatic reasoning failures in intermediate steps; second, a formal examination of the fixed prover problem as a theoretically underspecified limitation of the PAV framework with a proposal toward adaptive prover curriculum.*

---

### Direction 1 — Pragmatic Process Rewards: Probing Reasoning Traces for Non-Correctness Objectives

The PAV framework defines progress as a change in the likelihood of producing a correct final answer — a clean definition when a binary verifier $R_{\text{ex}} \in \{0, 1\}$ exists, as in mathematical reasoning. But it is not the only meaningful notion of progress during reasoning. A model can make steady progress toward a correct answer while generating intermediate steps that exploit a scalar implicature, silently resolve an ambiguity in its own favor, or build on a false axiom. None of these failures are detectable by $A^\mu$ as defined in the paper, because the prover's signal is entirely downstream of final correctness.

**The extension.** The three-signal RLVR objective developed in earlier entries (2026-03-04 and 2026-03-09) introduces two additional terms beyond correctness: a clarification reward $r_{\text{clarify}}$ weighted by an estimated ambiguity probe $\hat{a}$, and a loophole penalty $r_{\text{loophole}}$ weighted by a goal-conflict probe $\hat{c}$. That objective was formulated for final outputs. The natural extension to the process reward setting replaces the sparse final signal with a dense step-level signal:

$$r_h^{\text{eff}} = Q^{\pi}(s_h, a_h) + \alpha \cdot A^{\mu}(s_h, a_h) + \sum_k \beta_k \cdot P_k(s_h, a_h)$$

where $P_k(s_h, a_h)$ is the output of probe $k$ applied to reasoning step $a_h$ at state $s_h$. Each $P_k$ targets a specific pragmatic failure mode: loophole exploitation, scalar implicature violations, entailment failures between consecutive steps, or false-premise introduction. The key structural property is that when no probes fire — unambiguous, pragmatically well-formed reasoning — the objective collapses to the standard PAV effective reward, making this a strict generalization of PAV rather than a competing approach.

**Why step-level probes are stronger than final-output probes.** In the final-output setting, probe weights $\lambda(\hat{a}, \hat{c})$ and $\mu(\hat{a}, \hat{c})$ produce a single aggregated signal over the entire reasoning chain with no indication of where the failure occurred. A probe that fires at step $h$ and feeds into $P_k(s_h, a_h)$ assigns the penalty at the exact causal location — the step that introduced the loophole thought or the false supposition. From a credit assignment perspective, this is strictly more informative than terminal penalization, and aligns with why PAV outperforms ORM-RL in the first place: dense, step-level signal beats sparse, terminal signal.

**The data challenge.** The primary bottleneck is training data for the probes $P_k$. Labeled examples of reasoning traces with pragmatic failure modes annotated at the step level do not currently exist. One construction route: use a strong frontier model prompted with a phenomenon-specific rubric to annotate reasoning traces generated by a weaker model. The rubric-based approach developed in the AskBench line of work provides a template — the rubric specifies exactly what phenomena to look for and provides a structured ground-truth label per step. A text-level classifier may suffice for phenomena that are linguistically detectable (scalar implicature, entailment failures); an activation-level probe may be necessary for behaviors that do not consistently surface in output text (silent loophole resolution, covert false-premise assumption).

**What makes this non-incremental.** The closest existing work trains outcome-level probes for specific behaviors but does not apply them as step-level process reward signals. The PAV framework provides exactly the infrastructure needed to absorb these probes into a principled training objective. The contribution is the combination: dense process rewards over reasoning traces with per-step probes targeting pragmatic failure modes, integrated into the three-signal RLVR framework. This opens up RL training for tasks where correctness alone is an insufficient training signal — a class that includes most language tasks outside of formal mathematics.

---

### Direction 2 — The Fixed Prover Problem: Toward an Adaptive Prover Curriculum

The paper's central theoretical result (Theorem 3.1) gives a lower bound on per-step policy improvement:

$$\mathbb{E}_{s \sim \rho}\left[V^{\pi_{t+1}}(s) - V^{\pi_t}(s)\right] \;\gtrsim\; \gamma \cdot \underbrace{\mathbb{E}_{s}\,\mathbb{V}_{a \sim \pi_t}\!\left[A^\mu(s, a)\right]}_{\text{distinguishability}} \;+\; \gamma \cdot \underbrace{\mathbb{E}_{s}\,\mathbb{E}_{a \sim \pi_t}\!\left[A^\mu(s,a)\,A^{\pi_t}(s,a)\right]}_{\text{alignment}}$$

Both terms depend on $\pi_t$ — the current base policy iterate. The distinguishability term measures how well the prover separates the base policy's actions; the alignment term penalizes cases where the prover's ranking of actions is systematically opposed to the base policy's own assessment. Both change as $\pi_t$ evolves through RL training.

**The core gap.** The paper trains the prover once — as $\text{Bo4}(\pi_0)$ of the initial SFT policy — and holds it fixed for the entire RL run. This is empirically validated by showing final accuracy gains, but the paper does not measure how the distinguishability or alignment terms evolve during training. As the base policy improves and surpasses the capability level of $\text{Bo4}(\pi_0)$, two failure modes can emerge silently: the variance term shrinks (the prover can no longer distinguish good from bad steps because the base has moved beyond the prover's BoK level), or the alignment term degrades (the base policy has shifted far enough that the prover's action rankings begin to conflict with the base's own gradient direction). Either failure mode pushes the effective reward toward ORM-RL equivalence mid-training, without any visible indication in the accuracy curve. The paper explicitly acknowledges this gap: *"we were not able to run experiments where the prover policy is dynamically updated on the fly."*

**Formalizing the complementarity condition.** Define $\mu$ as $(\epsilon, \delta)$-complementary to base policy $\pi$ if:

$$\mathbb{E}_{s}\,\mathbb{V}_{a \sim \pi}\!\left[A^\mu(s, a)\right] \geq \epsilon \qquad \text{and} \qquad \mathbb{E}_{s}\,\mathbb{E}_{a \sim \pi}\!\left[A^\mu(s,a)\,A^\pi(s,a)\right] \geq -\delta$$

Under this definition, Theorem 3.1 guarantees non-trivial per-step improvement so long as the prover remains $(\epsilon, \delta)$-complementary to the current iterate. As RL proceeds, $\pi_t$ moves away from $\pi_0$, and $\text{Bo4}(\pi_0)$ may eventually fall below the $(\epsilon, \delta)$ threshold. At that point the process reward degrades — silently.

**An adaptive curriculum.** The natural remedy is to monitor $\mathbb{V}_{a \sim \pi_t}[A^\mu(s,a)]$ during training on a small held-out problem set and trigger a prover update — retraining $\mu$ as $\text{Bo4}(\pi_t)$ against the current base — whenever the variance falls below $\epsilon$. This is a theory-grounded criterion for prover curriculum scheduling, as opposed to the current practice of fixing the prover arbitrarily at initialization. Open empirical questions follow immediately: How often does retraining prove necessary? Does the answer depend on the difficulty distribution of training problems (harder problems shift the base policy's frontier faster, requiring more frequent prover updates)? Does retraining the prover introduce instability, since the PAV itself also needs to be retrained against the new prover?

**The two-player game.** The adaptive curriculum points toward the general problem the paper gestures at but leaves open: simultaneously optimizing the prover and the base as a two-player game. Formally, the prover $\mu$ is updated to maximize complementarity — maximizing the distinguishability term subject to the alignment constraint — while the base $\pi$ maximizes expected return under the effective reward. This is not a standard cooperative multi-agent formulation. The coupling between the two players is structural: the prover's objective is a function of the base policy's current action distribution, which itself changes as the base improves. Whether this joint optimization converges, at what rate, and to what fixed point is a fully open question. A convergence result, even under idealized tabular assumptions with oracle advantage estimates, would be a meaningful theoretical contribution. The empirical question — do adaptive provers sustain PAV-RL's sample efficiency gains throughout training rather than only in early iterations — is tractable with existing infrastructure and would directly address the paper's stated limitation.

---

### References

[1] Setlur, Nagpal, Fisch, Geng, Eisenstein, Agarwal, Agarwal, Berant & Kumar. **Rewarding Progress: Scaling Automated Process Verifiers for LLM Reasoning.** *ICLR 2025.* [https://arxiv.org/abs/2410.08146](https://arxiv.org/abs/2410.08146)

[2] Lightman, Kosaraju, Burda, Edwards, Baker, Lee, Leike, Schulman, Sutskever & Cobbe. **Let's Verify Step by Step.** *arXiv 2023.* [https://arxiv.org/abs/2305.20050](https://arxiv.org/abs/2305.20050)

[3] Wang, Li, Shao, Xu, Dai, Li, Chen, Wu & Sui. **Math-Shepherd: Verify and Reinforce LLMs Step-by-Step without Human Annotations.** *arXiv 2024.*

[4] Snell, Lee, Xu & Kumar. **Scaling LLM Test-Time Compute Optimally Can Be More Effective than Scaling Model Parameters.** *arXiv 2024.* [https://arxiv.org/abs/2408.03314](https://arxiv.org/abs/2408.03314)

[5] Zhao, Fang & Cheng. **When and What to Ask: AskBench and Rubric-Guided RLVR for LLM Clarification.** *arXiv 2026.* [https://arxiv.org/abs/2602.11199](https://arxiv.org/abs/2602.11199)

[6] Choi, Bansal & Stengel-Eskin. **Language Models Identify Ambiguities and Exploit Loopholes.** *EMNLP 2025.* [https://arxiv.org/abs/2508.19546](https://arxiv.org/abs/2508.19546)
