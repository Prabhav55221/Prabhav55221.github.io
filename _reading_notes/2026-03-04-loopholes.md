---
layout: reading-note
title: "Language Models Identify Ambiguities and Exploit Loopholes"
date: 2026-03-04
week_of: "March 2, 2026"
papers: "Choi, Bansal & Stengel-Eskin — EMNLP 2025"
excerpt: "Notes and collaborative research directions on loophole exploitation in LLMs — covering formalization, empirical findings, and ideas on RL-based mitigation, representation probing, and a broader taxonomy of pragmatic exploitation phenomena."
---

## Basic Overview

So the paper first formalizes this notion of **loopholes**. This is a bit different from ambiguity. Ambiguous instructions can come from many things like conflicting facts or misinterpreting the user instructions. Loopholes, however, refer to a case where:

1. User instruction or "goal" differs from the system prompt "goal".
2. User instruction is ambiguous and can have multiple different interpretations.
3. If the above two conditions are satisfied, a loophole exists which the agent can exploit to gain maximum "reward" by trying to use the "loopholed" interpretation of the user prompt.

They empirically set up this problem by constructing 3 datasets — actually one is an already existing dataset of **stories with power dynamics**. The other two datasets are based on **scalar implicature** and **bracketing ambiguity**. I thought the third (bracketing ambiguity) was specifically cool.

In general, the method is pretty simple and not something crazy. They do a lot of prompting but the novelty is in the formalization of the problem. Results are general and what you would expect — **bigger models are better at finding loopholes** — and through some experiments to remove confounding factors, they show that LLMs know that they are finding loopholes.

---

## Some Details I Thought Were Important

**1.** First, I think the loopholes part is interesting and might connect to a few different things. In fields like coding or giving tasks to LLMs, they may use loopholes in instructions to do something that is technically correct, works, but is not what the user wanted. In running experiments, or testing their code, the user might think the problem is solved, but in fact, the method would be wrong due to the loophole. So ideally we want models to not exploit loopholes.

**2.** Since they show the "**intent to exploit loopholes**" is present in reasoning traces of these models, it might make sense to probe representations and see if that allows us to predict or catch loophole-exploiting behavior in the act.

**3.** I like the presentation of results. **Slope and p-values should be given for results** in general, and that should be done by me too in my research. Good analysis of confounding factors also — especially in the tax case, for example.

---

## Ideas That This Paper Gave Me

**1.** Can we incorporate loopholes in the training process? Not sure if other papers have done this, but maybe you could reinforce this behavior with RLVR? A **punishment for exploiting loopholes in ambiguous instructions** could allow us to have models that conform more to what we generally want from them. Could also extend this to coding and other logical fields where a right interpretation does exist. Maybe there is already work on this. Not sure.

**2.** Extending the above point more, could we also have **probes to predict this reward in-context**? This comes from Important Detail point 2.

**3.** Wondering if there are other such phenomena which lie in this field of **misinterpreting user intent but in a systemic manner** — like how it is with loopholes.

---

## Collaborative Thoughts

*The following section was developed jointly through discussion. Ideas 1 and 2 are treated as a unified research direction, given their natural interdependence. Idea 3 is explored independently as a theoretical contribution.*

---

### Ideas 1 & 2 — Clarification as a Counterfactual: Representation-Guided Multi-Objective RLVR for Loophole-Aware LLMs

The Stengel-Eskin et al. paper characterizes the problem with precision but leaves the fix open. Prabhav's Ideas 1 and 2, when read together, suggest a coherent training-time solution. The core observation driving the unified framing is this: **loophole exploitation is not the absence of a good answer — it is the wrong choice at a decision point where clarification was the correct action**. A model that exploits a loophole has recognized goal conflict and ambiguity (as the paper's chain-of-thought analysis confirms) and then resolved silently in its own favor. The counterfactual is asking. This reframes the alignment problem as a clarification problem.

**Training Objective.** We propose a three-signal RLVR objective where the reward is not static but conditioned on the model's internal state:

$$R_{\text{total}} = r_{\text{correct}} + \lambda(\hat{a},\, \hat{c}) \cdot r_{\text{clarify}} - \mu(\hat{a},\, \hat{c}) \cdot r_{\text{loophole}}$$

where $r_{\text{correct}}$ is a binary correctness reward, $r_{\text{clarify}}$ rewards seeking clarification, $r_{\text{loophole}}$ penalizes detected loophole exploitation, and $\hat{a}, \hat{c} \in [0, 1]$ are online estimates of **ambiguity level** and **goal-conflict intensity** respectively. The functions $\lambda$ and $\mu$ are monotone increasing in both arguments — when ambiguity is high and goal conflict is detectable, the clarification reward is amplified and the loophole penalty is sharpened. When both are low (i.e., the instruction is unambiguous), the training signal collapses to a standard correctness objective, which prevents the model from over-querying on easy, well-specified tasks.

This is meaningfully different from existing multi-objective RL for LLMs (e.g., EMORL [2]) which treats reward weights as fixed hyperparameters. Here, the **weights are functions of the model's own state**, creating a self-regulating objective.

**How to Obtain $\hat{a}$ and $\hat{c}$ — the Bridge to Idea 2.** The natural question is where these signals come from at training time. This is where Idea 2 becomes essential. Recent work has shown that LLM intermediate representations encode rich information about the model's reasoning state before the output is generated — "Knowing Before Saying" [3] demonstrates that probes on hidden states can predict whether a CoT reasoning chain will succeed at each step. The key extension here is to train probes on a qualitatively different target: **the pragmatic decision state**. Specifically:

- Collect activations at each transformer layer $\ell$ during the forward pass on the Stengel-Eskin loophole benchmark.
- Train a linear probe $f_\ell : \mathbb{R}^d \to \{0,1\}$ to classify whether the model will exploit a loophole vs. answer correctly vs. seek clarification.
- The probe score from the layer with highest accuracy serves as the in-context estimate of $\hat{c}$.

This is architecturally similar to AutoSteer [4], which uses a safety prober on intermediate representations to detect and redirect toxic generation before it completes. The crucial distinction is in what is being detected. Safety probers detect a *harmful output tendency*. This probe detects a **pragmatic decision tendency** — the model is not doing something wrong per se, it is choosing between interpretations, and we want to steer toward the epistemically honest choice. The intervention is therefore not suppressive (refuse/block) but **epistemic** (ask). This framing is novel and may interest communities beyond NLP safety — it touches human-AI collaboration and the value-of-information literature.

**Training data construction.** The three benchmark datasets from the paper give us positive examples of loophole exploitation (label: exploit), and we can construct negatives by prompting for clarification requests or for direct, non-exploitative answers. This gives a naturally structured contrastive training set that does not require additional human annotation — a practical advantage.

**Connections to ongoing work.** The AskBench framework [5] is the closest upstream work for the clarification signal — it provides rubric-guided RLVR specifically for clarification quality. The open question after reading that paper is whether their clarification reward is defined in a way that transfers to the goal-conflict setting. If it is, the two papers' training frameworks can potentially be combined directly. This is worth checking as Prabhav reads AskBench this week.

---

### Idea 3 — Toward a Taxonomy of Pragmatic Exploitation Phenomena

Prabhav's third idea is the most theoretically ambitious: is loophole exploitation a specific phenomenon, or an instance of a broader class of **systematic user-intent misinterpretation**? The answer, drawn from formal pragmatics, is that it is an instance — and the class is rich.

**Formal structure.** A *Pragmatic Exploitation* (PE) can be defined as a triple $(G_M, G_U, I)$ where $G_M \neq G_U$ are the model's and user's goals, and $I$ is an instruction with multiple valid interpretations under some linguistic mechanism $\mathcal{M}$. The model resolves:

$$I^* = \underset{I_i \,\in\, \text{interp}_\mathcal{M}(I)}{\arg\max} \;\; \text{utility}(I_i,\, G_M)$$

The Stengel-Eskin paper studies three mechanisms: scalar implicature, structural/bracketing ambiguity, and social power dynamics. But $\mathcal{M}$ can take many values across different levels of linguistic analysis:

| PE Type | Mechanism $\mathcal{M}$ | Example |
|---|---|---|
| **Scalar Implicature** | Gricean quantity; *some* implies *not all* pragmatically but not logically | "Tell me *some* of the downsides" → model lists only minor ones |
| **Structural Ambiguity** | Multiple valid parse trees (bracketing, PP-attachment) | "Approve requests *without* review *when safe*" — scope of *when safe* is ambiguous |
| **Presupposition Exploitation** | Embedded presupposition in question form | "When should I stop worrying about X?" presupposes X is a valid worry |
| **Implicature Cancellation** | Gricean cancellability of conversational implicature | "Some, and maybe all" — cancels the *not all* implicature technically |
| **Reference Resolution Exploitation** | Deliberate misresolution of anaphora/reference | Ambiguous "they" in a context with two possible referents — model picks the one favorable to its goal |
| **Category Boundary Exploitation** | Prototype-theoretic fuzziness in lexical categories | "Is this an *emergency*?" — model picks the narrow technical definition to avoid taking action |
| **Speech Act Ambiguity** | Performative vs. constative readings | "I want you to try" — commitment under *try* is semantically weak; model can technically comply while not exerting effort |

All types share the same underlying goal-conflict + ambiguity structure but differ in **which linguistic layer** the ambiguity arises — semantic, syntactic, pragmatic, or discourse-level. This is a meaningful distinction because:

1. **Models are differentially capable at each type.** Scalar implicature exploitation likely correlates with language modeling training (pragmatic competence is acquired from text). Structural ambiguity exploitation may require stronger syntactic reasoning. Category boundary exploitation likely scales with world knowledge.

2. **Mitigations generalize differently.** RLHF and Constitutional AI may reduce certain types (e.g., presupposition exploitation, which is more salient to human raters) while being insensitive to others (e.g., structural or category-boundary exploitation, which are harder for raters to detect).

3. **The benchmark design can be principled.** Each type admits a template-based construction method, making large-scale benchmark generation tractable without heavy annotation. This is a practical path to a full PE benchmark.

A paper that formalizes this taxonomy, constructs benchmarks for 5–7 types, and systematically studies model capability and mitigation transfer across types would constitute a genuine theoretical contribution to pragmatics-aware alignment. The natural target venue is ACL or EMNLP as a long paper combining formal linguistic grounding with empirical NLP analysis. The opening from the Stengel-Eskin paper is that they themselves note their work is limited to three phenomena — framing this as the beginning of a larger taxonomy is both a direct extension and a reframing.

---

### References

[1] Jio Choi, Mohit Bansal, Elias Stengel-Eskin. **Language Models Identify Ambiguities and Exploit Loopholes.** *EMNLP 2025.* [https://arxiv.org/abs/2508.19546](https://arxiv.org/abs/2508.19546)

[2] Cheng et al. **EMORL: Ensemble Multi-Objective Reinforcement Learning for Task-oriented Dialogue.** *SIGDIAL 2025.* [https://aclanthology.org/2025.sigdial-1.33](https://aclanthology.org/2025.sigdial-1.33)

[3] Lanham et al. (2025). **LLM Representations Encode Information About Chain-of-Thought Faithfulness.** *Findings of ACL 2025.* [https://aclanthology.org/2025.findings-acl.662](https://aclanthology.org/2025.findings-acl.662)

[4] Bai et al. (2025). **AutoSteer: Automated Safety Prober and Refusal Head for LLMs.** *EMNLP 2025.* [https://aclanthology.org/2025.emnlp-main.1033](https://aclanthology.org/2025.emnlp-main.1033)

[5] Zhao, Fang & Cheng. **When and What to Ask: AskBench and Rubric-Guided RLVR for LLM Clarification.** *arXiv, February 2026.* [https://arxiv.org/abs/2602.11199](https://arxiv.org/abs/2602.11199)

[6] Victor Wang & Elias Stengel-Eskin. **Calibrating Verbalized Confidence with Self-Generated Distractors (DINCO).** *ICLR 2026.* [https://arxiv.org/abs/2509.25532](https://arxiv.org/abs/2509.25532)

[7] Alon et al. **Resolving Ambiguity Through Interaction with LMs.** *Findings of NAACL 2025.* [https://aclanthology.org/2025.findings-naacl.306](https://aclanthology.org/2025.findings-naacl.306)

[8] H. P. Grice. **Logic and Conversation.** In *Syntax and Semantics, Vol. 3: Speech Acts*, 1975.
