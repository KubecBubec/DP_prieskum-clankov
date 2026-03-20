# Realistic Evaluation of Model Merging for Compositional Generalization

## Fáza A: bibliografická karta

- Názov: Realistic Evaluation of Model Merging for Compositional Generalization
- Autori: Derek Tam, Yash Kant, Brian Lester, Igor Gilitschenski, Colin Raffel
- Rok: 2024
- Typ článku: arXiv preprint
- Link: <https://arxiv.org/abs/2409.18314>
- Alternatívny link: <https://openreview.net/forum?id=Bq3fEAGXUL>
- Téma: Evaluation / benchmarking of merged models
- Relevancia pre DP: `4/5`

## 1) Motivácia autorov výskumu

Autori reagujú na rýchly rast počtu merge metód, ktoré sú často hodnotené v neporovnateľných experimentálnych nastaveniach. Rôzne práce používajú iné modely, iné datasety, iné predpoklady o dostupnosti dát a iné rozpočty na výpočet, čo výrazne sťažuje reálne porovnanie.

Motiváciou článku je preto vytvoriť `realistickejší` evaluačný rámec. Namiesto toho, aby sa sústredili len na výkon na held-in taskoch, kladú dôraz na `compositional generalization`, teda na schopnosť merged modelu kombinovať schopnosti expertov na nových, nevidených kombináciách úloh, domén alebo jazykov.

## 2) Lokalizácia analýzy literatúry

Analýza literatúry sa nachádza najmä v `1 Introduction`, `2.1 Merging Methods` a `2.2 Challenges in Comparing Merging Methods`.

Autori rozoberajú širokú paletu merge techník:

- `Simple Averaging`,
- `SLERP/MLERP`,
- `Task Arithmetic`,
- `DARE`,
- `TIES`,
- `Fisher Merging`,
- `RegMean`,
- `MaTS`.

Ich kľúčové vymedzenie voči literatúre je, že doterajšie práce sa často líšia v:

- cieľoch hodnotenia,
- použitých modeloch a datasetoch,
- dostupných prerequisites,
- výpočtových nárokoch,
- a hyperparameter tuning požiadavkách.

Článok tak nadväzuje na literatúru skôr ako metaevaluačný rámec: nesnaží sa presadiť jednu novú merge techniku, ale nastaviť spravodlivejšie a praktickejšie porovnanie.

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je zistiť, ako si rôzne merge metódy vedú v jednotnom, realistickom a prakticky orientovanom evaluačnom nastavení.

Hlavnú výskumnú otázku možno formulovať takto:

**Ktoré merge metódy reálne podporujú compositional generalization, ak ich porovnávame na rovnakých modeloch, úlohách, výpočtových rozpočtoch a praktických predpokladoch?**

Vedľajšie otázky sú:

- ako sa líši vzťah medzi held-in výkonom a generalizáciou podľa modality,
- ako sa metódy správajú pri raste počtu merge-nutých modelov,
- aké prerequisites a výpočtové náklady jednotlivé metódy vyžadujú,
- a aká je ich citlivosť na hyperparametre.

## 4) Koncepčný rámec článku

Koncepčný rámec článku je postavený na rozlíšení:

- `held-in multitask performance`,
- a `compositional generalization`.

Autori tvrdia, že práve compositional generalization je realistickejšia motivácia pre model merging. Ak merge len zachová výkon na pôvodných taskoch, používateľ môže stále siahnuť po pôvodných expert modeloch. Skutočná pridaná hodnota merge-u vzniká vtedy, keď merged model dokáže riešiť nové kombinácie schopností, ktoré jednotlivé modely samy nevedia.

Tento rámec aplikujú na tri modality:

- cross-domain image classification,
- image generation,
- cross-lingual NLP.

Tým článok posúva diskusiu od „vie merge zložiť multitask model?“ k otázke „vie merge vytvoriť novú kompozičnú schopnosť?“

## 5) Použitá metóda (stratégia) výskumu

Ide o `benchmarkovo-empirický` článok so silným dôrazom na praktické podmienky použitia merge metód.

Autori porovnávajú osem populárnych metód:

- `Simple Averaging`,
- `SLERP/MLERP`,
- `Task Arithmetic`,
- `DARE`,
- `TIES`,
- `Fisher Merging`,
- `RegMean`,
- `MaTS`.

Metódy nehodnotia len podľa výkonu, ale aj podľa:

- potrebných vstupov a štatistík,
- potreby prístupu k pretrained modelu,
- potreby validačných alebo tréningových dát,
- výpočtových nákladov,
- a hyperparameter sensitivity.

Výsledky zasadzujú aj do kontextu troch dôležitých baseline:

- `pretrained model`,
- `individual task models`,
- `multitask model` trénovaný na všetkých held-in dátach naraz.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Autori používajú tri evaluačné prostredia:

- `cross-domain image classification`,
- `image generation`,
- `cross-lingual natural language processing`.

Pre vision používajú `DomainNet`, kde každá kombinácia `(category, domain)` tvorí task. Hodnotia:

- held-in tasky na diagonále,
- a unseen kompozície kategórií a domén mimo diagonály.

Pre image classification fine-tunujú `CLIP ViT-B/32`.
Pre image generation fine-tunujú `Stable Diffusion 2.1` pomocou `LoRA`.

Pre NLP používajú viacjazyčné `(task, language)` kombinácie nad:

- question answering,
- natural language inference,
- summarization,
- word sense disambiguation,
- answerability,

s jazykmi ako:

- `English`,
- `Arabic`,
- `Thai`,
- `German`,
- `Korean`.

Ako base model pre NLP používajú `mT5-xl-lm-adapt`.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Analýza pokrýva päť hlavných osí:

- held-in výkon,
- compositional generalization,
- praktické prerequisites,
- výpočtové náklady,
- hyperparameter sensitivity,
- a scaling s počtom merge-nutých modelov.

Autori preto nesledujú len presnosť alebo skóre, ale aj:

- či metóda potrebuje pretrained model,
- či potrebuje auxiliary statistics,
- či potrebuje dáta počas merge-u,
- koľko FLOPs si merge vyžiada,
- a ako robustná je voči voľbe hyperparametrov.

To je silná stránka článku, lebo praktická použiteľnosť merge metódy môže byť úplne iná než naznačuje samotný benchmark score.

## Hlavné zistenia

Článok ukazuje, že správanie merge metód je silne závislé od modality a že vysoký held-in výkon automaticky neznamená dobrú compositional generalization.

Hlavné zistenia sú:

- pri image classification a image generation existuje voľná pozitívna korelácia medzi held-in výkonom a generalizáciou,
- pri cross-lingual NLP sú held-in výkon a generalizácia naopak `anticorrelated`, takže metóda dobrá na multitask retention môže byť slabá na compositional generalization,
- pri image generation viaceré merge metódy prekonávajú multitask training tak na held-in, ako aj na generalizačných úlohách,
- pri NLP väčšina merge metód v generalizácii zaostáva aj za pretrained modelom,
- `RegMean` a `MaTS` sú silné na held-in výkon pre image classification a NLP, ale ich výhoda sa môže stratiť pri generalizácii,
- `Fisher Merging` niekedy generalizuje lepšie než RegMean a MaTS, hoci teoreticky riešia podobný cieľ,
- drahšie merge metódy často dosahujú lepší held-in výkon, ale nie nevyhnutne lepšiu compositional generalization,
- výpočtové náklady sa medzi metódami líšia takmer o dva rády,
- `Task Arithmetic` a `TIES` sú citlivé na scaling hyperparameter `lambda`,
- `DARE` je po zvolení dobrého `lambda` robustnejší voči dropout pravdepodobnosti,
- `Simple Averaging` a `Fisher Merging` sú rozumné voľby tam, kde nie je možný tuning hyperparametrov,
- s rastúcim počtom merge-nutých modelov zvyčajne klesá held-in multitask výkon, ale generalizácia na unseen kombinácie sa môže zlepšovať.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Článok je pre DP veľmi užitočný ako metodologická protiváha k paperom, ktoré reportujú len priaznivé merge výsledky na held-in benchmarkoch.

Je relevantný najmä tým, že:

- zavádza pojem `realistic evaluation`,
- kladie dôraz na `compositional generalization`,
- ukazuje, že kvalita merge metódy závisí od modality a cieľa,
- detailne rozoberá praktické predpoklady a cenu merge metód,
- a pomáha interpretovať, prečo výsledky rôznych merge paperov bývajú ťažko porovnateľné.

Pre diplomovú prácu je to silný článok k časti o hodnotení a k diskusii, čo vlastne znamená „dobrý merge“.

## Dôkazové citácie / oporné časti článku

- Motivácia:
  - "merging methods are rarely evaluated in the same experimental setting"
  - "merging methods impose different requirements in terms of computational costs, data availability, and hyperparameter tuning"

- Hlavná myšlienka:
  - "we primarily focus on measuring generalization to new tasks that require the compositional combination of capabilities"
  - "compositional generalization provides a more realistic motivation for merging"

- Kľúčové zistenia:
  - "for image classification and generation, we observe a loose positive correlation between a merging method's held-in and generalization performance"
  - "for NLP, the held-in and generalization performance is anticorrelated"
  - "increasing the number of models being merged tends to result in worse multitask performance on held-in tasks but better generalization performance on unseen tasks"

- Praktická hodnota:
  - "we explicitly enumerate extra requirements of each method, including hyperparameters that require tuning, the amount of compute required, and whether auxiliary data is required"
  - "the cost of different merging methods varies by almost two orders of magnitude"

- Interpretácia:
  - "merged models can exhibit better generalization to new domains than the multitask and pretrained models"

