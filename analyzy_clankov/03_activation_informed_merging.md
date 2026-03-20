# Activation-Informed Merging of Large Language Models

## Fáza A: bibliografická karta

- Názov: Activation-Informed Merging of Large Language Models
- Autori: Amin Heyrani Nobari, Kaveh Alim, Ali ArjomandBigdeli, Akash Srivastava, Faez Ahmed, Navid Azizan
- Rok: 2025
- Typ článku: NeurIPS 2025 poster + arXiv
- Link: <https://arxiv.org/abs/2502.02421>
- Alternatívny link: <https://openreview.net/forum?id=T4qJuQCFAK>
- Téma: Model merging / model soups
- Relevancia pre DP: `5/5`

## 1) Motivácia autorov výskumu

Autori vychádzajú z rastúceho počtu task-specialized LLMs fine-tuned z rovnakého base modelu, ktoré sa v praxi používajú izolovane, hoci každé z nich nesie špecializované znalosti. Motiváciou článku je nájsť spôsob, ako tieto expert modely zlúčiť do jedného modelu tak, aby sa zlepšil multitask výkon bez dodatočného tréningu a bez výraznej straty pôvodných schopností base modelu.

Zároveň autori upozorňujú, že väčšina merge metód pracuje iba vo weight space a ignoruje activation space, hoci ten sa už osvedčil pri pruning a quantization. Merge preto reinterpretujú aj cez optiku continual learning: pri zlučovaní môže dochádzať k degradácii a strate všeobecných schopností base modelu, teda k javu podobnému catastrophic forgetting.

## 2) Lokalizácia analýzy literatúry

Analýza súvisiacej literatúry je sústredená najmä v sekcii `2 Background and Related Work`, ktorá má tri hlavné línie:

- literatúra o `LLM merging`,
- literatúra o `continual learning`,
- literatúra o `model compression`, najmä activation-aware prístupy.

Autori tu prepájajú foundational merging práce ako model soups, task arithmetic, TIES, DARE, WIDEN, Fisher merging a RegMean s myšlienkou, že activation-space informácia by mohla byť užitočná aj pri merge. Súčasne zasadzujú AIM do rámca regularization-based continual learning a nadväzujú na Activation-aware Weight Quantization (`AWQ`).

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je navrhnúť a overiť merge stratégiu pre LLMs, ktorá bude využívať activation-space informáciu base modelu na zlepšenie kvality merged modelu.

Hlavnú výskumnú otázku možno vyjadriť takto:

**Môže activation-space informácia pomôcť pri model merging tak, aby merged LLM lepšie zachoval dôležité schopnosti base modelu a zároveň efektívnejšie kombinoval znalosti viacerých fine-tuned expertov?**

Čiastkové otázky sú:

- či sa dá activation-informed relaxácia aplikovať ako doplnok k existujúcim merge metódam,
- či takýto prístup zlepší výkon na viacerých benchmarkoch naraz,
- či je metóda robustná voči voľbe veľkosti calibration setu,
- či sa prístup generalizuje aj mimo jednej architektúry a mimo čisto textových modelov.

## 4) Koncepčný rámec článku

Koncepčný rámec článku stojí na spojení troch myšlienkových línií:

- `model merging` ako kombinovanie znalostí viacerých fine-tuned expertov do jedného checkpointu,
- `continual learning` ako rámec na pochopenie rizika forgettingu pri odchýlení sa od base modelu,
- `activation-aware analysis`, kde magnitúdy aktivácií signalizujú dôležitosť jednotlivých váh.

Kľúčová idea AIM je, že nie všetky váhy by sa pri merge mali meniť rovnako. Váhy, ktoré sú podľa aktivácií base modelu salentné, majú byť viac chránené, zatiaľ čo menej dôležité váhy môžu absorbovať väčšiu časť merge update. AIM je teda `algorithm-agnostic relaxation scheme`, ktorá moduluje už existujúci merge update podľa activation-based saliency mapy a hyperparametra `omega`.

## 5) Použitá metóda (stratégia) výskumu

Ide o `experimentálny kvantitatívny výskum` s návrhom novej merge metódy a jej systematickým porovnaním voči viacerým baseline prístupom.

Autori najprv definujú AIM ako doplnkový mechanizmus nad existujúcimi merge algoritmami. Postup je v zásade tento:

- z task-agnostic calibration corpus pustia vstupy cez base model,
- z aktivácií odhadnú dôležitosť kanálov a tým aj saliency váh,
- existujúci merge update `Delta_merged` následne relaxujú tak, aby sa salentné váhy base modelu menili menej,
- silu tejto relaxácie riadia hyperparametrom `omega`.

Metóda je navrhnutá tak, aby bola použiteľná nad rôznymi merging metódami bez potreby meniť ich vnútorný algoritmus. Okrem activation-based formulácie autori skúmajú aj sensitivity-based variant založený na gradientoch, no zdôrazňujú, že activation-based AIM je výpočtovo lacnejší.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Dáta pre vyhodnotenie vznikajú kombináciou calibration dát a benchmarkového testovania merged modelov.

Na výpočet activation saliency používajú:

- task-agnostic calibration set zo `validation subsetu The Pile`,
- v hlavných experimentoch `256 sekvencií`, pričom v ablácii testujú aj výrazne menšie množstvá.

Pre hlavné textové experimenty používajú expert modely fine-tuned z `Llama-2-13B`, konkrétne:

- `WizardLM-13B` pre instruction following,
- `WizardMath-13B` pre matematické reasoning,
- `llama-2-13b-code-alpaca` ako code expert.

Merge vyhodnocujú na `6 benchmarkoch`:

- `MMLU`,
- `IFEval`,
- `HumanEval`,
- `MBPP`,
- `MATH`,
- `GSM8K`.

Dodatočne testujú generalizáciu aj na multimodálnej architektúre `Qwen 2.5-VL-7B`, kde mergeujú video-reasoning a CAD expert modely a vyhodnocujú ich na ďalšej sade šiestich benchmarkov vrátane `Video MMMU`, `VSI-Bench` a CAD-Coder benchmarkov.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Autori používajú viacúrovňovú kvantitatívnu komparatívnu analýzu.

Na úrovni jednotlivých úloh sledujú benchmark performance na šiestich testoch. Zároveň však argumentujú, že pri merging nestačí sledovať iba izolované skóre, preto zavádzajú aj agregovanú metriku `HV Gain` (`Hypervolume Gain`) z multi-objective optimalizačnej perspektívy.

Analýza teda zahŕňa:

- porovnanie AIM-augmented merge modelov s pôvodnými merge modelmi,
- porovnanie naprieč `5 merging methods`,
- porovnanie naprieč rôznymi scenármi s rôznym počtom expertov,
- analýzu Pareto frontier a hypervolume gain,
- abláciu hyperparametra `omega`,
- abláciu veľkosti calibration setu,
- doplnkové porovnanie activation-based a sensitivity-based formulácie.

Táto metodika je silná najmä v tom, že nehodnotí iba jednu metriku, ale kombináciu per-benchmark výkonu a celkovej multitask trade-off kvality merged modelu.

## Hlavné zistenia

Článok ukazuje, že activation-space informácia je pri model merging prakticky užitočná a nie iba teoreticky zaujímavá.

Hlavné zistenia sú:

- `AIM` konzistentne zlepšuje výkon existujúcich merge metód naprieč benchmarkmi,
- vo väčšine prípadov zvyšuje aj `HV Gain`, teda kvalitu merged modelu z multitask pohľadu,
- autori uvádzajú pri niektorých benchmarkoch zlepšenie až `o 40 %`,
- všetci top performeri v ich hlavných tabuľkách sú AIM-augmented merged models,
- výhody sa prenášajú aj mimo Llama-2 textových modelov na `Qwen 2.5-VL-7B`, teda aj na inú architektúru a multimodálne nastavenie,
- metóda je robustná na malý calibration set a výkon sa stabilizuje už pri veľmi malom objeme dát,
- vhodná voľba `omega` je približne v pásme `0.4 až 0.6`, pričom `0.4` sa ukazuje ako rozumný kompromis.

Najdôležitejšie zistenie pre DP je, že quality merging nemusí byť riadený iba geometriou vo weight space; activation-space informácia môže slúžiť ako účinný signál na ochranu dôležitých parametrov a stabilizáciu kombinácie znalostí.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Tento článok je pre DP mimoriadne vhodný a patrí medzi najsilnejšie priamo zamerané zdroje v celom zozname.

Jeho prínos pre tému `Kombinácia znalostí veľkých jazykových modelov` je vysoký, pretože:

- rieši priamo `LLM model merging`,
- prináša novú konkrétnu metódu kombinácie znalostí,
- prepája weight-space merge s activation-space signálmi,
- interpretuje merge aj cez problém `catastrophic forgetting`,
- hodnotí výsledky na viacerých benchmarkoch a cez multi-objective metriku.

Pre DP je cenný aj tým, že posúva diskusiu od jednoduchého averagingu k informovanejšiemu merge mechanizmu. Je to teda veľmi dobrý most medzi foundational merging prácami a novšími LLM-specific metódami, ktoré pracujú robustnejšie a cielenejšie.

## Dôkazové citácie / oporné časti článku

- Motivácia:
  - "an important opportunity is missed, as these fine-tuned task-specialized models typically operate in isolation without leveraging the rich features that each possesses"
  - "in the vast majority of cases, merging LLMs is done using algorithms that explore the weight space of models and do not leverage the information in the activation space"

- Hlavná myšlienka AIM:
  - "we introduce a new method named Activation-Informed Merging (AIM), which modifies the update step in the merging process to ensure that the most influential weights of the base model, identified through its activations, undergo minimal changes"
  - "AIM seeks to achieve this by relaxing the changes to the salient weights of the base model in the final merged model"

- Continual learning rámec:
  - "we view the model merging problem through the lens of CL, primarily focusing on regularization-based methods"
  - "to avoid catastrophic forgetting of the base model’s abilities"

- Dáta a benchmarky:
  - "We choose the calibration dataset to be a subset of the validation data from the pile dataset"
  - "we measure model performance on ... MMLU ... IFEval ... HumanEval and MBPP ... MATH and GSM8K"

- Hlavné výsledky:
  - "the vast majority of cases, AIM causes a significant performance boost"
  - "AIM boosted merged model performance by up to 40% in some cases"
  - "AIM is highly robust"

- Generalizácia:
  - "AIM’s benefits are not architecture-specific and generalize effectively to multimodal models"

