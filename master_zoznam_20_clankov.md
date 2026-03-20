# Master zoznam 20 článkov pre DP

## Kontext

Tento súbor obsahuje finálny pracovný zoznam `20 vybraných článkov` pre diplomovú prácu na tému:

**Kombinácia znalostí veľkých jazykových modelov**

Články sú rozdelené do `5 tematických sekcií`, pričom z každej témy boli vybrané `4 odporúčané články`.

Účel tohto súboru:

- mať na jednom mieste finálny zoznam článkov,
- použiť ho ako checklist na stiahnutie PDF/verzií článkov,
- následne podľa neho spracovať analýzu každého článku do samostatného súboru.

---

## Téma 1: Model merging / model soups

### 1. Model soups: averaging weights of multiple fine-tuned models improves accuracy without increasing inference time

- Rok: 2022
- Link: <https://arxiv.org/abs/2203.05482>
- Poznámka: foundational článok k model soups a weight averaging

### 2. Knowledge is a Region in Weight Space for Fine-tuned Language Models

- Rok: 2023
- Link: <https://aclanthology.org/2023.findings-emnlp.95/>
- Alternatívny link: <https://arxiv.org/html/2302.04863>
- Poznámka: dôležitý článok o štruktúre znalostí vo weight space language modelov

### 3. Activation-Informed Merging of Large Language Models

- Rok: 2025
- Link: <https://arxiv.org/abs/2502.02421>
- Alternatívny link: <https://openreview.net/forum?id=T4qJuQCFAK>
- Poznámka: LLM-specific merging článok s aktivačne riadeným merge

### 4. Model Merging in Pre-training of Large Language Models

- Rok: 2025
- Link: <https://arxiv.org/abs/2505.12082>
- Alternatívny link: <https://openreview.net/forum?id=HW55AwGEC8>
- Poznámka: rozšírenie merge pohľadu do pre-training fázy LLM

---

## Téma 2: Task vectors / editing in weight space

### 5. Editing Models with Task Arithmetic

- Rok: 2022
- Link: <https://arxiv.org/abs/2212.04089>
- Alternatívny link: <https://iclr.cc/virtual/2023/poster/12254>
- Poznámka: foundational článok pre task vectors a task arithmetic

### 6. Task Arithmetic in the Tangent Space: Improved Editing of Pre-Trained Models

- Rok: 2023
- Link: <https://arxiv.org/abs/2305.12827>
- Alternatívny link: <https://openreview.net/forum?id=0A9f2jZDGW>
- Poznámka: teoreticko-empirické rozšírenie task arithmetic

### 7. Layer-Aware Task Arithmetic: Disentangling Task-Specific and Instruction-Following Knowledge

- Rok: 2025
- Link: <https://arxiv.org/abs/2502.20186>
- Alternatívny link: <https://aclanthology.org/2025.findings-emnlp.644/>
- Poznámka: moderný LLM-specific task arithmetic článok

### 8. When is Task Vector Provably Effective for Model Editing? A Generalization Analysis of Nonlinear Transformers

- Rok: 2025
- Link: <https://arxiv.org/abs/2504.10957>
- Alternatívny link: <https://openreview.net/forum?id=vRvVVb0NAz>
- Poznámka: teoretický článok o podmienkach úspechu task vectors

---

## Téma 3: Combining fine-tuned checkpoints

### 9. Training-free LLM Merging for Multi-task Learning

- Rok: 2025
- Link: <https://arxiv.org/abs/2506.12379>
- Alternatívny link: <https://aclanthology.org/2025.acl-long.1588/>
- Poznámka: veľmi silný článok o training-free merge špecializovaných LLM

### 10. Model Merging by Uncertainty-Based Gradient Matching

- Rok: 2024
- Link: <https://arxiv.org/abs/2310.12808>
- Alternatívny link: <https://openreview.net/forum?id=D7KJmfEDQP>
- Poznámka: metodický článok o merge cez gradient mismatch a uncertainty

### 11. SuperMerge: An Approach For Gradient-Based Model Merging

- Rok: 2024
- Link: <https://arxiv.org/abs/2412.10416>
- Alternatívny link: <https://openreview.net/forum?id=lIdc5DUplq>
- Poznámka: gradient-based merge viacerých fine-tuned modelov

### 12. Safeguard Fine-Tuned LLMs Through Pre- and Post-Tuning Model Merging

- Rok: 2024
- Link: <https://arxiv.org/abs/2412.19512>
- Alternatívny link: <https://aclanthology.org/2025.findings-emnlp.901/>
- Poznámka: merge ako nástroj na zachovanie safety/alignment po fine-tuningu

---

## Téma 4: Catastrophic forgetting vs transfer

### 13. TIES-Merging: Resolving Interference When Merging Models

- Rok: 2023
- Link: <https://arxiv.org/abs/2306.01708>
- Alternatívny link: <https://openreview.net/forum?id=cP7mbBUn1E>
- Poznámka: kľúčový článok o interferencii pri model merging

### 14. Mitigating Catastrophic Forgetting in Language Transfer via Model Merging

- Rok: 2024
- Link: <https://arxiv.org/abs/2407.08699>
- Poznámka: priame prepojenie model merging a catastrophic forgetting

### 15. Merge to Learn: Efficiently Adding Skills to Language Models with Model Merging

- Rok: 2024
- Link: <https://arxiv.org/abs/2410.12937>
- Alternatívny link: <https://aclanthology.org/2024.findings-emnlp.915/>
- Poznámka: pridávanie nových skillov pri zachovaní starých schopností

### 16. Whoever Started the Interference Should End It: Guiding Data-Free Model Merging via Task Vectors

- Rok: 2025
- Link: <https://arxiv.org/abs/2503.08099>
- Alternatívny link: <https://openreview.net/forum?id=xR9msNaREW>
- Poznámka: data-free merge s dôrazom na minimalizáciu interferencie

---

## Téma 5: Evaluation / benchmarking of merged models

### 17. MergeBench: A Benchmark for Merging Domain-Specialized LLMs

- Rok: 2025
- Link: <https://arxiv.org/abs/2505.10833>
- Poznámka: najdôležitejší benchmark paper pre domain-specialized LLM merging

### 18. A Systematic Study of Model Merging Techniques in Large Language Models

- Rok: 2025
- Link: <https://arxiv.org/abs/2511.21437>
- Poznámka: systematické porovnanie merging techník na LLM

### 19. What Matters for Model Merging at Scale?

- Rok: 2024
- Link: <https://arxiv.org/abs/2410.03617>
- Alternatívny link: <https://openreview.net/forum?id=9sbetmvNpW>
- Poznámka: scaling laws a podmienky úspešného merge

### 20. Realistic Evaluation of Model Merging for Compositional Generalization

- Rok: 2024
- Link: <https://arxiv.org/abs/2409.18314>
- Alternatívny link: <https://openreview.net/forum?id=Bq3fEAGXUL>
- Poznámka: evaluation framework zameraný na compositional generalization



