# MergeBench: A Benchmark for Merging Domain-Specialized LLMs

## Fáza A: bibliografická karta

- Názov: MergeBench: A Benchmark for Merging Domain-Specialized LLMs
- Autori: Yifei He, Siqi Zeng, Yuzheng Hu, Rui Yang, Tong Zhang, Han Zhao
- Rok: 2025
- Typ článku: arXiv preprint
- Link: <https://arxiv.org/abs/2505.10833>
- Téma: Evaluation / benchmarking of merged models
- Relevancia pre DP: `5/5`

## 1) Motivácia autorov výskumu

Autori vychádzajú z toho, že model merging sa rýchlo rozvíja, no existujúce evaluačné nastavenia sú príliš malé a úzke na to, aby ukázali skutočné správanie merge metód na moderných LLM. Väčšina skorších prác testuje malé modely a relatívne plytké NLP úlohy, takže nie je jasné, ako sa merge správa pri väčších, domain-specialized jazykových modeloch.

Motiváciou článku je preto vytvoriť benchmark, ktorý bude reprezentatívny pre reálne použitie: viac modelových rodín, väčšie modely, viacero dôležitých domén, štandardizované fine-tuning protokoly a viac než len finálne task skóre. Autori chcú benchmarkom zodpovedať aj praktické otázky, napríklad koľko merge stojí, koľko zabúda a kedy sa oplatí viac `mix data` než `merge models`.

## 2) Lokalizácia analýzy literatúry

Analýza literatúry sa nachádza hlavne v `1 Introduction`, `2 Background`, `5 Related Works` a v porovnávacej `Table 4`.

Autori mapujú:

- klasické merge metódy založené na `coefficient tuning`,
- novšie prístupy založené na `sparsification`,
- a predchádzajúce benchmarky model merging-u.

V rámci benchmarkovaných metód zahŕňajú:

- `Model Soup`,
- `Task Arithmetic`,
- `Fisher Merging`,
- `RegMean`,
- `TIES`,
- `DARE`,
- `Consensus TA`,
- `Localize-and-Stitch` a jeho dataless variant.

Voči predchádzajúcim evaluačným prácam sa vyhraňujú tým, že staršie benchmarky často nemajú:

- rôzne modelové rodiny,
- veľké modely nad 7B,
- reálne LLM doménové úlohy,
- podporu gradient-based a statistic-based metód,
- alebo plnú open-source reprodukovateľnosť.

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je vybudovať štandardizovaný benchmark na porovnávanie merge techník pre domain-specialized LLMs a pomocou neho odvodiť praktické odporúčania pre výber algoritmov.

Hlavnú výskumnú otázku možno formulovať takto:

**Ako sa existujúce model merging techniky správajú na moderných open-source LLM naprieč viacerými doménami, ak ich hodnotíme nielen podľa multi-task výkonu, ale aj podľa forgettingu a runtime nákladov?**

S tým súvisia ďalšie otázky:

- ktoré metódy fungujú najlepšie na väčších a silnejších base modeloch,
- akú úlohu hrá tuning merge koeficientov a sparsifikácia,
- ako sa merge porovnáva s multitask trainingom,
- a ktoré algoritmy dávajú najlepší pomer výkon/cena v praxi.

## 4) Koncepčný rámec článku

Koncepčný rámec MergeBench stojí na troch evaluačných osiach:

- `multi-task performance`,
- `retention of base model knowledge / forgetting`,
- `runtime efficiency`.

Benchmark vychádza z myšlienky, že kvalitné posúdenie merge metód nemá merať len to, ako dobre merged model vykonáva cieľové úlohy, ale aj:

- koľko z pôvodných generalizačných schopností base modelu si zachová,
- a akú výpočtovú cenu si vyžiada samotný merge vrátane hyperparameter tuning-u.

Autori preto konštruujú:

- osem base modelov z rodín `Llama` a `Gemma`,
- päť špecializovaných domén,
- štyridsať špecializovaných modelov,
- a jednotný tréningovo-evaluačný protokol.

Benchmark je navrhnutý tak, aby čo najlepšie izoloval javy ako `skill interference`, `knowledge retention` a `efficiency-effectiveness trade-off`.

## 5) Použitá metóda (stratégia) výskumu

Ide o `benchmarkovo-empirický` článok.

Autori nevytvárajú novú merge metódu, ale:

1. vyberú moderné open-source base modely,
2. fine-tunujú ich na piatich doménach do špecializovaných expert modelov,
3. aplikujú na ne osem reprezentatívnych merge algoritmov,
4. vyhodnocujú merged modely na jednotnej eval suite,
5. a osobitne merajú aj forgetting a wall-clock runtime.

Kľúčová metodická hodnota je štandardizácia:

- rovnaké base modely,
- rovnaké task kategórie,
- rovnaké tréningové a evaluačné pravidlá,
- zjednotené dátové požiadavky metód,
- transparentné meranie validačného aj algoritmického času.

Pri metódach, ktoré potrebujú auxiliary data alebo validačné dáta, autori tieto požiadavky zjednocujú, aby bolo porovnanie férové.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Benchmark pokrýva päť domén:

- `instruction following`,
- `mathematics`,
- `multilingual understanding`,
- `coding`,
- `safety`.

Použité base modely zahŕňajú:

- `Llama-3.2-3B`,
- `Llama-3.1-8B`,
- `Gemma-2-2B`,
- `Gemma-2-9B`,
- plus ich instruction-tuned varianty.

Pre tréning používajú napríklad:

- `TULU-3 persona IF`,
- `DART-Math`,
- `NuminaMath-TIR`,
- `Aya`,
- `Magicoder`,
- `WildGuardMix`,
- `WildJailbreak`.

Pre evaluačnú sadu používajú okrem iného:

- `IFEval`,
- `GSM8K`,
- `MATH`,
- `M_MMLU`,
- `M_ARC`,
- `M_Hellaswag`,
- `HumanEval+`,
- `MBPP+`,
- `WildGuardTest`,
- `HarmBench`,
- `DoAnythingNow`,
- `XSTest`.

Na meranie forgettingu pridávajú aj benchmarky mimo piatich hlavných domén, napríklad:

- `MMLU`,
- `TriviaQA`,
- `SQuADv2`,
- `CoQA`,
- `PubMedQA`,
- `WMT14 Fr-En`.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Autori analyzujú merge metódy v troch režimoch:

- výkon na cieľových doménach,
- zachovanie všeobecného vedomia base modelu,
- runtime a validačné náklady.

Ako hlavné metriky používajú:

- `normalized performance` voči zodpovedajúcim špecializovaným expert modelom,
- `normalized generalization` voči base modelu pri forgetting analýze,
- a `wall-clock time`, rozdelený na `algorithm runtime` a `validation runtime`.

Dôležitý analytický moment je, že validačný čas neignorujú. Ukazujú, že pri viacerých merge metódach môže byť práve tuning hyperparametrov dominantnou časťou celkových nákladov, čo zásadne mení praktické porovnanie algoritmov.

## Hlavné zistenia

Článok ukazuje, že správanie merge metód závisí silno od sily base modelu, typu regularizácie aj praktických nákladov.

Hlavné zistenia sú:

- model merging funguje lepšie na silnejších base modeloch; väčšie a lepšie instruction-tuned modely dosahujú výrazne vyššiu normalized performance,
- na 8B a 9B modeloch sa merge metódy často dostávajú nad `90%` výkonu špecializovaných expert modelov,
- `Localize-and-Stitch` a jeho dataless variant patria medzi najsilnejšie metódy na multi-task performance,
- `RegMean` je konkurencieschopný najmä na menších modeloch, ale jeho výhoda sa na väčších modeloch zmenšuje,
- `TIES`, `Task Arithmetic` a `Consensus TA` tvoria stredne silnú skupinu, pričom výrazne profitujú z instruction-tuned base modelov,
- merged modely často lepšie zachovávajú base model knowledge než multitask-trained modely, najmä vďaka `coefficient tuning` a `sparsification`,
- menšie scaling koeficienty a sparsity stratégie pomáhajú znižovať forgetting,
- `DARE` síce dosahuje slušné skóre, ale jeho random dropping horšie zachováva base model knowledge,
- `Model Soup` je najlacnejší, ale nie vždy najsilnejší,
- z hľadiska praktického pomeru výkon/cena vychádzajú dobre `Localize-and-Stitch`, `RegMean` a `Task Arithmetic`,
- vysoké validačné náklady robia `TIES` a `DARE` menej atraktívnymi v resource-constrained prostrediach,
- multi-task learning stále býva silnejší na čisto in-domain výkon pri ne-konfliktných a vyvážených taskoch, takže merge nie je univerzálnou náhradou za joint training.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Tento článok je pre DP jeden z najdôležitejších, pretože poskytuje evaluačný rámec, v ktorom možno čítať a porovnávať ostatné merge práce systematicky, nie len po jednotlivých ad-hoc experimentoch.

Je veľmi relevantný najmä tým, že:

- benchmarkuje moderné `domain-specialized LLMs`,
- porovnáva viac rodín modelov a viac merge techník naraz,
- explicitne hodnotí `forgetting`,
- pridáva aj `runtime efficiency`,
- a dáva praktické odporúčania, kedy siahnuť po jednoduchých a kedy po silnejších merge metódach.

Pre diplomovú prácu je to kľúčový oporný článok pre kapitolu o hodnotení a benchmarkingu model merging-u.

## Dôkazové citácie / oporné časti článku

- Motivácia:
  - "existing evaluations are limited in both model scale and task diversity"
  - "leaving open questions about their applicability to large, domain-specialized LLMs"

- Prínos benchmarku:
  - "we introduce MergeBench, a comprehensive evaluation suite designed to assess model merging at scale"
  - "we standardize finetuning and evaluation protocols"

- Kľúčové osi hodnotenia:
  - "assess eight representative merging methods across multi-task performance, forgetting and runtime efficiency"
  - "together, these evaluations provide a complete picture of the trade-offs between utility, robustness and computational efficiency"

- Hlavné zistenia:
  - "model merging tends to perform better on stronger base models"
  - "techniques such as scaling coefficient tuning and sparsification help preserve pretrained knowledge"
  - "multi-task models still achieve stronger in-domain performance"

- Praktické odporúčania:
  - "validation cost is often overlooked in prior work, we find it can dominate total runtime"
  - "Start with Model Soup for its extremely low-cost merging"
  - "If validation data are available, try Dataless Localize-and-Stitch or Task Arithmetic"

