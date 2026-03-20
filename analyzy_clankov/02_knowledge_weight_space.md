# Knowledge is a Region in Weight Space for Fine-tuned Language Models

## Fáza A: bibliografická karta

- Názov: Knowledge is a Region in Weight Space for Fine-tuned Language Models
- Autori: Almog Gueta, Elad Venezian, Colin Raffel, Noam Slonim, Yoav Katz, Leshem Choshen
- Rok: 2023
- Typ článku: Findings of EMNLP + arXiv
- Link: <https://aclanthology.org/2023.findings-emnlp.95/>
- Alternatívny link: <https://arxiv.org/html/2302.04863>
- Téma: Model merging / model soups
- Relevancia pre DP: `4/5`

## 1) Motivácia autorov výskumu

Autori vychádzajú z toho, že výskum neurónových sietí sa tradične sústreďuje na jeden model natrénovaný na jednom datasete, zatiaľ čo vzťahy medzi rôznymi fine-tuned modelmi zostávajú slabo preskúmané. Zaujíma ich najmä to, ako spolu súvisia modely fine-tuned na rôznych datasetoch alebo úlohách a čo tieto vzťahy hovoria o reprezentácii znalostí v priestore váh.

Motiváciou článku je teda pochopiť, či znalosti modelu nie sú naviazané len na jednotlivé body v priestore váh, ale na širšie oblasti. Ak by sa ukázalo, že vysokovýkonné fine-tuned modely ležia v určitých regiónoch priestoru váh, malo by to silné dôsledky pre model merging, interpoláciu modelov a výber lepších inicializácií pre ďalší fine-tuning.

## 2) Lokalizácia analýzy literatúry

Analýza súvisiacej literatúry sa nachádza v záverečnej časti článku, najmä v sekcii `8`, na ktorú autori výslovne odkazujú v úvode formuláciou, že vzťah k predchádzajúcim prácam diskutujú v `§8`.

Okrem toho sa odkazy na relevantné práce objavujú priebežne aj v analytických sekciách, najmä pri:

- lineárnej konektivite medzi modelmi,
- interpolácii medzi fine-tuned modelmi,
- model averaging a model fusion prácach.

Related work teda nie je len krátka izolovaná sekcia, ale je previazaná aj s interpretáciou ich vlastných zistení.

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je zistiť, ako sú v priestore váh usporiadané fine-tuned language models a či dobre fungujúce modely vytvárajú súvislé regióny s nízkou chybou.

Hlavnú výskumnú otázku možno formulovať takto:

**Predstavujú fine-tuned language models vysokého výkonu izolované body v priestore váh, alebo skôr dobre definované regióny, v ktorých aj ďalšie body dosahujú vysoký výkon?**

S tým súvisia aj ďalšie čiastkové otázky:

- či modely fine-tuned na rovnakom datasete vytvárajú kompaktné zhluky,
- či modely z rovnakého task family tvoria širšie task-level regióny,
- či interpolované alebo priemerované modely vo vnútri týchto regiónov dosahujú porovnateľný alebo lepší výkon,
- či je výhodné začať ďalší fine-tuning z centra takého regiónu namiesto pôvodného pretrained modelu.

## 4) Koncepčný rámec článku

Koncepčný rámec článku je postavený na týchto ideách:

- **weight space perspektíva**, kde je každý model reprezentovaný ako bod/vektor v priestore váh,
- **loss landscape perspektíva**, podľa ktorej môžu podobne fine-tuned modely ležať v spojitých oblastiach s nízkou chybou,
- **granularita podobnosti modelov**, ktorú autori skúmajú na troch úrovniach:
  - rovnaký dataset,
  - rovnaká task family,
  - všeobecný jazykový región,
- **interpolácia a convex hull medzi modelmi**, ktoré slúžia ako prostriedok na testovanie, či sa znalosti rozprestierajú v celom regióne, nie len v jednotlivých bodoch.

V tomto rámci je "knowledge" chápané nie ako vlastnosť jedného konkrétneho checkpointu, ale ako priestorová vlastnosť regiónu v weight space.

## 5) Použitá metóda (stratégia) výskumu

Ide o **experimentálny kvantitatívny výskum** kombinovaný s geometrickou analýzou priestoru váh.

Autori robia dva hlavné typy experimentov:

- trénujú väčší počet fine-tuned modelov s rôznymi charakteristikami a analyzujú ich rozloženie v priestore váh pomocou clusteringu,
- porovnávajú loss a výkon modelov v rámci regiónov, medzi regiónmi a pri interpolácii/averagingu medzi modelmi.

Hlavné experimentálne stratégie zahŕňajú:

- clustering modelov fine-tuned na rovnakých a príbuzných datasetoch,
- lineárnu interpoláciu medzi dvojicami modelov,
- sampling modelov z convex hull medzi skupinami podobných modelov,
- extrapoláciu mimo regiónu na test hraníc basin,
- test následného fine-tuningu zo stredu regiónu.

Ide teda o výskum, ktorý je metodicky veľmi blízky weight-space analýze a model merging literatúre.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Dáta pre vyhodnotenie vznikajú experimentálne cez fine-tuning a následné meranie loss/accuracy na benchmarkových datasetoch.

Autori uvádzajú, že:

- fine-tunujú a vyhodnocujú modely na `36 datasetoch`,
- datasety zahŕňajú viacero rodín úloh:
  - `NLI`,
  - sentiment analysis,
  - topic classification,
  - Twitter domain,
  - širší `General` set z GLUE a SuperGLUE,
- ako základný pretrained model používajú najmä `RoBERTa-base`,
- používajú viac seedov, pričom niektoré experimenty analyzujú 5 seedov a clustering na úrovni toho istého datasetu až 20 seedov.

Metódou získania dát je teda:

- systematické vytváranie veľkého počtu fine-tuned modelov,
- záznam ich polohy v priestore váh,
- meranie loss a výkonnosti pri interpolácii, samplingu regiónov a ďalšom fine-tuningu.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Autori používajú **kvantitatívnu geometricko-komparatívnu analýzu**.

Kľúčové analytické postupy sú:

- clustering modelov v priestore váh a meranie clustering accuracy,
- porovnanie loss medzi skupinami `In`, `In'` a `Ex`,
- lineárna interpolácia medzi dvojicami modelov,
- sampling modelov z convex hull regiónu,
- extrapolácia za hranice regiónu na test stability,
- použitie metriky `PB` na porovnanie pravdepodobnosti, že model z jedného regiónu outperformuje model z iného regiónu.

Autori analyzujú výsledky na troch úrovniach granularity:

- per dataset,
- per task,
- všeobecný jazykový región.

Primárnymi analytickými signálmi sú:

- loss,
- clustering accuracy,
- porovnávacie pravdepodobnosti typu `PB`,
- následne aj accuracy improvement pri ďalšom fine-tuningu zo stredu regiónu.

## Hlavné zistenia

Článok prináša niekoľko veľmi silných zistení:

- fine-tuned modely na rovnakom datasete tvoria tesné zhluky v priestore váh,
- modely fine-tuned na datasetoch rovnakého task family tvoria širšie, ale stále rozpoznateľné regióny,
- body medzi podobnými modelmi, vrátane interpolovaných modelov a modelov zo sampled convex hull, často dosahujú porovnateľný alebo lepší výkon než pôvodné fine-tuned modely,
- extrapolácia mimo týchto regiónov vedie rýchlo k degradácii výkonu, čo naznačuje, že ide o ohraničené low-loss oblasti,
- začiatok ďalšieho fine-tuningu zo stredu regiónu je rovnako dobrý alebo lepší než štart z pôvodného pretrained modelu; autori uvádzajú zlepšenie v `11 z 12 datasetov` s priemerným nárastom presnosti `3.06`.

Najdôležitejšie zistenie pre DP je, že znalosti modelu sa nejavia ako izolované body, ale ako regióny v priestore váh, čo dáva silný teoreticko-empirický základ pre merging a averaging prístupy.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Článok je pre DP veľmi hodnotný, aj keď nejde o "merge metódu" v úzkom zmysle.

Jeho hlavná hodnota pre tému `Kombinácia znalostí veľkých jazykových modelov` spočíva v tom, že:

- poskytuje silný koncepčný základ pre uvažovanie o kombinácii modelov v `weight space`,
- ukazuje, že averaging a interpolácia môžu fungovať preto, že high-performing modely ležia v zmysluplných regiónoch,
- prepája language model doménu s geometrickou interpretáciou weight-space transferu,
- podporuje predstavu, že model positioned between similar models can acquire knowledge of both.

Pre analytickú časť DP je preto tento článok dôležitý najmä ako:

- teoreticko-empirické zdôvodnenie weight-space merging prístupov,
- most medzi foundational model soups literatúrou a LLM/task-vector/checkpoint-merging literatúrou.

## Dôkazové citácie / oporné časti článku

- Motivácia a problém:
  - "relatively little is known about the relationships between different models, particularly those trained or tested on different datasets."
  - "we address this by studying how the weight space and the underlying loss landscape of different models are interconnected."

- Hlavná téza:
  - "finetuned models that were optimized for high performance, reside in well-defined regions in weight space, and vice versa – that any model that resides anywhere in those regions also exhibits high performance."

- Koncepčný rámec regiónov:
  - "Fine-tuning ends up in a region determined by the dataset ... which resides in the task ... and language tasks regions."
  - "the whole region that lies between these models (their convex hull) corresponds to models that perform well."

- Metóda:
  - "We conduct two main types of experiments. In one we train models ... and examine their representation in weight space using clustering. In the second experiment type, we compare losses of one group of models to another."

- Dáta:
  - "We finetune and evaluate models on 36 datasets."
  - "We experiment with RoBERTa-base ... as our base pretrained model."

- Hlavné zistenia:
  - "interpolated models perform comparably or even better than the models they are created from."
  - "starting from the center of the region is as effective, if not more, than using the pretrained model in 11 out of 12 datasets, resulting in an average accuracy improvement of 3.06."

