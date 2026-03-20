# Training-free LLM Merging for Multi-task Learning

## Fáza A: bibliografická karta

- Názov: Training-free LLM Merging for Multi-task Learning
- Autori: Zichuan Fu, Xian Wu, Yejing Wang, Wanyu Wang, Shanshan Ye, Hongzhi Yin, Yi Chang, Yefeng Zheng, Xiangyu Zhao
- Rok: 2025
- Typ článku: ACL 2025 + arXiv
- Link: <https://arxiv.org/abs/2506.12379>
- Alternatívny link: <https://aclanthology.org/2025.acl-long.1588/>
- Téma: Combining fine-tuned checkpoints
- Relevancia pre DP: `5/5`

## 1) Motivácia autorov výskumu

Autori vychádzajú z rýchlo rastúceho počtu open-source, task-specific a language-specific fine-tuned LLMs. Tieto modely predstavujú bohatý zdroj špecializovaných znalostí, no v praxi sa často používajú oddelene, čo komplikuje nasadenie a zvyšuje náklady.

Motiváciou článku je zistiť, či možno tieto špecializované modely zlúčiť do jedného `unified` modelu bez ďalšieho tréningu. Autori zároveň upozorňujú, že training-free merging naráža na dva hlavné problémy: `noise` z fine-tuningu a `knowledge misalignment` medzi nezávisle trénovanými modelmi.

## 2) Lokalizácia analýzy literatúry

Analýza súvisiacej literatúry sa opiera najmä o sekcie `2 Preliminary for LLM Merging` a `5 Related Works`.

Autori rozlišujú viacero skupín predchádzajúcich prístupov:

- `weighted averaging` metódy ako `Model Soups`,
- `delta vector-based` prístupy ako `Task Arithmetic`, `TIES`, `DARE`, `DELLA`, `Model Breadcrumbs`,
- príbuzné `knowledge transfer` metódy ako `OT-Fusion` a `Layer Swapping`,
- a klasické `multi-task fine-tuning` baseline.

Článok sa voči nim vymedzuje tým, že nechce robiť ďalšie doladenie po merge, ale navrhuje riadené, `training-free` odstránenie konfliktov na úrovni modelu aj vrstiev.

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je navrhnúť training-free merge metódu, ktorá spoľahlivo skombinuje špecializované LLMs do jedného multitask modelu.

Hlavnú výskumnú otázku možno formulovať takto:

**Je možné bez ďalšieho tréningu zlúčiť rôzne fine-tuned LLMs tak, aby výsledný model získal multi-task a prípadne multilingual schopnosti a zároveň minimalizoval interferenciu medzi znalosťami?**

Autori túto otázku rozkladajú na viac scenárov:

- merging toho istého tasku v rôznych jazykoch,
- merging rôznych taskov v tom istom jazyku,
- merging rôznych taskov aj jazykov naraz,
- generalizáciu na ďalšie open-source doménové modely.

## 4) Koncepčný rámec článku

Koncepčný rámec článku stojí na dvoch hlavných zdrojoch zlyhania pri model merging:

- `noise` a prehnané parameter updates vzniknuté počas fine-tuningu,
- `knowledge alignment conflicts`, teda nekompatibilné zmeny vo vrstvách spôsobené odlišnými optimalizačnými trajektóriami.

Na tomto základe autori navrhujú `Hi-Merging` (`Hierarchical Iterative Merging`), ktorý má dve úrovne:

- `model-wise pruning and scaling`,
- `layer-wise pruning and scaling`.

Kľúčovým konceptom je `contribution analysis`, ktorá meria prínos jednotlivých vrstiev pre schopnosti modelu a zároveň identifikuje konfliktné vrstvy. Merge tak už nie je iba globálne sčítanie delta vektorov, ale hierarchicky riadené odstraňovanie konfliktov.

## 5) Použitá metóda (stratégia) výskumu

Ide o `experimentálny kvantitatívny výskum` s návrhom novej training-free merge metódy.

Metóda `Hi-Merging` má dva kroky:

- `model-wise pruning and scaling`, kde sa z delta vectorov odstránia malé/noisy parametre a zníži sa vplyv príliš ostrých zmien,
- `layer-wise pruning and scaling`, kde sa iteratívne riešia konfliktné vrstvy podľa contribution analysis.

V model-wise časti autori používajú:

- `pruning threshold p`,
- `scaling factor s`.

V layer-wise časti definujú:

- `deletion impact`,
- `addition impact`,
- ich súčet ako `contribution`,
- a z neho odvodený `conflict score`.

Potom podľa typu konfliktu rozlišujú:

- `severe conflict`,
- `partial conflict`,
- `mutual enhancement`.

Takto postupne upravujú len tie vrstvy, ktoré reálne kazia výsledný merge.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Autori vyhodnocujú metódu na štyroch datasetoch pokrývajúcich:

- `angličtinu` a `čínštinu`,
- `multiple-choice QA (MCQA)`,
- `open-domain QA`.

V hlavných experimentoch používajú základný model:

- `Qwen2-7B-Instruct`.

Pre fine-tuning používajú `LoRA` s konkrétnymi nastaveniami a následne merge vykonávajú cez `mergekit`.

Vyhodnotenie prebieha cez:

- `accuracy` pri MCQA,
- `BLEU-4`,
- `ROUGE-1`,
- `ROUGE-2`,
- `ROUGE-L` pri QA,
- plus agregované metriky ako `Avg Impr.` a `Avg Rank.`.

Dodatočne testujú aj:

- iné foundation modely v dodatkoch,
- merging dvoch open-source medicínskych modelov,
- a ďalší merge medicínskeho modelu s matematickým modelom.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Autori používajú `kvantitatívnu komparatívnu analýzu` naprieč viacerými merge scenármi.

Porovnávajú:

- pre-trained model,
- jednotlivé fine-tuned modely,
- multi-task fine-tuned model,
- a širokú sadu training-free merge baseline metód.

Analýza je rozdelená podľa výskumných otázok:

- `bilingual task merging`,
- `monolingual multi-task merging`,
- `bilingual multi-task merging`,
- open-source domain merge,
- case study layer conflicts.

Okrem číselných metrík článok obsahuje aj interpretačnú analýzu layer-wise konfliktov, kde autori vizualizujú príspevky vrstiev a ukazujú, že konflikty nie sú po sieti rozložené rovnomerne.

## Hlavné zistenia

Článok ukazuje, že dobre navrhnutý `training-free` merge môže byť pre multitask LLM veľmi silný.

Hlavné zistenia sú:

- `Hi-Merging` konzistentne prekonáva klasické merge baseline ako `Model Soups`, `Task Arithmetic`, `TIES`, `DARE`, `DELLA` či `Model Breadcrumbs`,
- v bilingválnom MCQA merge dosahuje najlepšiu priemernú výkonnosť a ranking,
- pri `monolingual multi-task merging` merged modely často prekonávajú aj multi-task fine-tuned baseline; autori uvádzajú pre `Hi-Merging` približne `1.84 %` relatívne zlepšenie oproti multi-task fine-tuned modelu,
- v `bilingual multi-task` scenároch Hi-Merging opäť dosahuje najlepšie alebo veľmi silné agregované výsledky,
- merging modelov z rovnakého alebo príbuzného doménového priestoru funguje lepšie než merge vzdialených domén; merge medicínskeho a matematického modelu už vedie k miernym stratám,
- case study ukazuje, že konflikty sú sústredené len v niektorých vrstvách, čo podporuje ich iteratívny layer-wise prístup.

Najdôležitejšie pre DP je, že článok ukazuje praktický a účinný spôsob kombinovania znalostí viacerých fine-tuned checkpointov bez retrainingu.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Tento článok je pre DP veľmi silný a patrí medzi najrelevantnejšie zdroje v celej sade.

Jeho hodnota pre tému `Kombinácia znalostí veľkých jazykových modelov` je vysoká, pretože:

- rieši priamo `training-free LLM merging`,
- pracuje s fine-tuned checkpointmi ako nositeľmi špecializovaných znalostí,
- navrhuje konkrétnu merge metódu, nie iba teoretický rámec,
- ukazuje merge naprieč taskami aj jazykmi,
- porovnáva sa s veľkou skupinou relevantných baseline metód.

Pre DP je tento článok veľmi dôležitý ako moderný, prakticky orientovaný dôkaz, že knowledge combination vo weight space môže fungovať bez ďalšieho tréningu, ak sa dobre ošetrí `noise` a `parameter conflict`.

## Dôkazové citácie / oporné časti článku

- Motivácia:
  - "is it possible to combine these specialized models to create a unified model with multi-task capabilities"
  - "current model merging methods face two fundamental challenges"

- Hlavná metóda:
  - "We introduces Hierarchical Iterative Merging (Hi-Merging), a training-free method"
  - "model-wise and layer-wise pruning and scaling, guided by contribution analysis, to mitigate parameter conflicts"

- Model-wise časť:
  - "pruning eliminates negligible parameter changes while scaling moderates the significant ones"
  - "p = 0.1, s = 0.9 ... can defeat the original model"

- Layer-wise časť:
  - "we iteratively merge the most conflicting layers"
  - "conflicts are not uniformly distributed"

- Hlavné výsledky:
  - "Hi-Merging consistently outperforms existing merging techniques"
  - "surpasses the performance of models fine-tuned on combined datasets in most scenarios"
  - "Hi-Merging approach achieves a 1.84% relative improvement over the multi-task fine-tuned model"

