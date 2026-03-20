# Safeguard Fine-Tuned LLMs Through Pre- and Post-Tuning Model Merging

## Fáza A: bibliografická karta

- Názov: Safeguard Fine-Tuned LLMs Through Pre- and Post-Tuning Model Merging
- Autori: Hua Farn Hsuan Su, Shachi H Kumar, Saurav Sahay, Shang-Tse Chen, Hung-yi Lee
- Rok: 2024
- Typ článku: Findings of EMNLP 2025 + arXiv
- Link: <https://arxiv.org/abs/2412.19512>
- Alternatívny link: <https://aclanthology.org/2025.findings-emnlp.901/>
- Téma: Combining fine-tuned checkpoints
- Relevancia pre DP: `4/5`

## 1) Motivácia autorov výskumu

Autori vychádzajú z problému, že fine-tuning safety-aligned LLM na downstream úlohy často vedie ku `catastrophic forgetting`, ktoré sa prejaví poklesom safety vlastností pôvodne zarovnaného modelu. Model po adaptácii síce získa lepší výkon na novej úlohe, ale môže začať generovať viac škodlivých alebo nežiadaných odpovedí.

Motiváciou článku je nájsť jednoduchý spôsob, ako zachovať safety bez potreby ďalších safety dát. Autori upozorňujú, že kvalitné alignment dáta použité pri pôvodnom safety tuningu zvyčajne nie sú verejne dostupné a náhradné syntetické safety dáta môžu mať nižšiu kvalitu alebo viesť k alignment driftu.

## 2) Lokalizácia analýzy literatúry

Analýza literatúry je sústredená najmä v sekciách `2.1 Catastrophic Forgetting and Safety Degradation in LLMs` a `2.2 Model Merging`.

Autori prepájajú dve línie výskumu:

- výskum safety degradation po fine-tuningu a metódy jej zmierňovania,
- a model merging / task vector prístupy.

V prvej línii spomínajú:

- prístupy s dodatočnými safety dátami,
- self-distillation,
- regularizačné stratégie počas tréningu,
- post-hoc re-alignment.

V druhej línii sa odvolávajú na:

- `weight averaging`,
- `SLERP`,
- `DARE`,
- `task vectors`,
- `safety vectors`,
- a `WiSE-FT`.

Ich pozícia je, že na rozdiel od mnohých safety re-alignment prístupov nepotrebujú ani extra safety dáta, ani prístup k pre-alignment checkpointu, ale iba dvojicu `aligned model` a `fine-tuned model`.

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je overiť, či jednoduché merge-nutie pôvodného aligned modelu s downstream fine-tuned modelom dokáže súčasne:

- zachovať alebo obnoviť safety,
- a pritom nepokaziť výkon na downstream úlohe.

Hlavnú výskumnú otázku možno sformulovať takto:

**Dá sa safety degradation po fine-tuningu zmierniť jednoduchým mergingom pre- a post-fine-tuned modelu bez použitia dodatočných safety dát?**

Vedľajšie otázky sú:

- či tento prístup funguje naprieč rôznymi modelovými rodinami,
- či funguje na rôznych downstream taskoch,
- ako sa správa pri rôznych veľkostiach modelov,
- a či pomáha zachovať aj iné capability aligned modelu, napríklad instruction-following.

## 4) Koncepčný rámec článku

Koncepčný rámec článku chápe safety degradation ako dôsledok `catastrophic forgetting` počas downstream fine-tuningu. Pôvodný aligned model obsahuje safety knowledge, ktorá sa pri adaptácii na novú úlohu čiastočne prepíše alebo oslabí.

Navrhnuté riešenie je mimoriadne jednoduché:

1. zobrať aligned model `theta_base`,
2. fine-tunovať ho na cieľovú úlohu a získať `theta_t`,
3. vytvoriť merged model interpoláciou:
   `theta_merged = (1 - lambda) theta_base + lambda theta_t`.

Koncepčne ide o kompromis medzi:

- zachovaním pôvodnej aligned reprezentácie,
- a prenesením task-specific znalostí z fine-tuned modelu.

Autori zdôrazňujú, že aj keď používajú lineárny merge, rámec je kompatibilný aj s pokročilejšími merging metódami ako `SLERP` alebo `DARE`.

## 5) Použitá metóda (stratégia) výskumu

Ide o `empiricko-experimentálny` článok s jednoduchým metodickým návrhom.

Postup má dve fázy:

- `pre-tuning`: vychádza sa z already aligned instruct modelu,
- `post-tuning`: model sa fine-tunuje na downstream task pomocou `LoRA`,
- následne sa oba checkpointy merge-nú cez interpolation coefficient `lambda`.

Ako hlavný variant v sekcii výsledkov používajú `Linear Merging`, ale dopĺňajú aj `SLERP` a `DARE`. Výber `lambda` robia podľa validačného výkonu.

Dôležité je, že ich prístup:

- nevyžaduje ďalší tréning po merge,
- nevyžaduje extra safety dáta,
- nevyžaduje externý safety model na úpravu váh,
- a dá sa chápať ako veľmi lacná post-hoc re-alignment stratégia.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Autori vyhodnocujú metódu na štyroch downstream taskoch:

- `reasoning`,
- `medical assistance`,
- `code generation`,
- `tool usage proficiency`.

Použité datasety a benchmarky:

- reasoning: `Flan V2` tréningové dáta, hodnotenie na `BBH`,
- medical assistance: `ChatDoctor`,
- code generation: `MagiCoder`, hodnotenie na `HumanEval`,
- tool usage: `OpenFunctions`.

Pre safety hodnotenie používajú:

- `AdvBench`,
- `HEx-PHI`,
- klasifikátor `WildGuard`,
- metriku `Attack Success Rate (ASR)`.

Testujú viacero aligned modelov:

- `LLaMA-3-8B-Instruct`,
- `Gemma-2-2B-It`,
- `Qwen2.5-7B-Instruct`,
- a pri analýze škálovania aj ďalšie veľkosti z rodín `Qwen2.5` a `Gemma-2`.

Každý model fine-tunujú na každú downstream úlohu pomocou `LoRA` s tromi rôznymi seedmi a reportované výsledky priemerujú.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Vyhodnotenie je založené na porovnaní trade-offu medzi downstream výkonom a safety.

Autori porovnávajú:

- `SFT` bez merge,
- `Weight Decay`,
- `Dropout`,
- `Linear Merging`,
- `DARE`,
- `SLERP`.

Sledujú najmä:

- downstream performance,
- `ASR` na `AdvBench`,
- `ASR` na `HEx-PHI`,
- Pareto front medzi výkonom a safety,
- správanie naprieč modelovými veľkosťami,
- a zachovanie `instruction-following` cez `IFEval`.

Analýza nie je len jednorazové porovnanie jedného bodu; autori skúmajú celé spektrum hyperparametrov a ukazujú, že merge vytvára hladké a kontrolovateľné Pareto fronty medzi utility a safety.

## Hlavné zistenia

Článok ukazuje, že jednoduchý merge aligned a fine-tuned modelu často výrazne zmierni safety degradation, pričom zachová alebo dokonca zlepší downstream výkon.

Hlavné zistenia sú:

- samotné `SFT` konzistentne zhoršuje safety naprieč modelmi a úlohami,
- `Dropout` a `Weight Decay` prinášajú len obmedzené zlepšenie a často nedokážu obnoviť safety aligned modelu,
- merge-based prístupy vo všeobecnosti fungujú lepšie než regularizačné baseline,
- `Linear Merging` často znižuje `ASR` takmer na úroveň pôvodného aligned modelu, pričom si drží vysoký task performance,
- prístup funguje naprieč viacerými modelovými rodinami a rôznymi veľkosťami modelov,
- safety degradation nemá jednoduchý monotónny vzťah k veľkosti modelu,
- merge nepomáha len so safety, ale aj so zachovaním `instruction-following` capability, ktoré sa po fine-tuningu zhoršujú,
- rozdiely medzi kategóriami v `HEx-PHI` ukazujú, že niektoré škodlivé oblasti sú voči safety degradation náchylnejšie než iné.

Pre DP je dôležité, že článok demonštruje praktický prípad, kde merge neslúži na multitask performance samotnú, ale na kombináciu task adaptation a zachovania bezpečnostného správania modelu.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Článok je pre DP veľmi zaujímavý, pretože ukazuje špecifický a prakticky dôležitý use-case model merging-u: zachovanie alignmentu a safety pri downstream adaptácii.

Je relevantný najmä preto, že:

- používa čistý `weight-level merging`,
- pracuje priamo s kombináciou `base aligned knowledge` a `task-specific knowledge`,
- rieši konflikt medzi prenosom novej schopnosti a zachovaním pôvodných vlastností modelu,
- prepája merging s témou `catastrophic forgetting vs transfer`,
- a ukazuje, že aj jednoduché interpolácie checkpointov môžu mať silný efekt.

Pre diplomovú prácu je to dobrý článok na rozšírenie pohľadu: merge nemusí slúžiť len na skladanie viacerých task expertov, ale aj na vyvažovanie utility a safety po fine-tuningu.

## Dôkazové citácie / oporné časti článku

- Motivácia:
  - "Fine-tuning large language models (LLMs) for downstream tasks often leads to catastrophic forgetting, notably degrading the safety of originally aligned models."
  - "How can we preserve safety while improving downstream task performance without additional safety data?"

- Hlavná myšlienka:
  - "We show that simply merging the weights of pre- and post-fine-tuned models effectively mitigates safety degradation while enhancing performance."
  - "our approach consists of two steps: (1) fine-tune the aligned model on the downstream task, and (2) merge the aligned model with the fine-tuned model"

- Výsledky:
  - "SFT consistently leads to safety degradation"
  - "the proposed approach consistently achieves a better balance between performance and safety"
  - "It often reduces ASR to levels near that of the aligned model while maintaining - or even improving - task performance."

- Zachovanie ďalších schopností:
  - "The proposed approach substantially restores performance to the level of the aligned model, indicating that merging can also preserve instruction adherence."

- Praktická hodnota:
  - "model merging is not only effective but also practical for mitigating safety degradation in real-world settings, even without access to additional safety data."

