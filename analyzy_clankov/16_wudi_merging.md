# Whoever Started the Interference Should End It: Guiding Data-Free Model Merging via Task Vectors

## Fáza A: bibliografická karta

- Názov: Whoever Started the Interference Should End It: Guiding Data-Free Model Merging via Task Vectors
- Autori: Runxi Cheng, Feng Xiong, Yongxian Wei, Wanyun Zhu, Chun Yuan
- Rok: 2025
- Typ článku: ICML 2025 + arXiv
- Link: <https://arxiv.org/abs/2503.08099>
- Alternatívny link: <https://openreview.net/forum?id=xR9msNaREW>
- Téma: Catastrophic forgetting vs transfer
- Relevancia pre DP: `4/5`

## 1) Motivácia autorov výskumu

Autori riešia problém, že pri data-free model merging-u ostáva medzi expert modelmi výrazná interferencia, ktorá spôsobuje pokles výkonu merged modelu. Hoci existujú silné metódy používajúce test-time dáta alebo dodatočné task-specific komponenty, tie strácajú praktickú výhodu jednoduchého data-free merge.

Motiváciou článku je preto nájsť spôsob, ako znížiť interferenciu úplne bez dodatočných dát, bez test-time adaptácie a bez extra storage. Autori chcú ukázať, že informácia potrebná na riadenie merge-u je už implicitne obsiahnutá v samotných `task vectors`.

## 2) Lokalizácia analýzy literatúry

Analýza literatúry je sústredená hlavne v sekcii `2 Related Work`.

Autori rozdeľujú existujúce práce do troch skupín:

- `data-free model merging`,
- `test-time adaptation model merging`,
- `MoE-like model merging`.

Do data-free línie zaraďujú najmä:

- `Simple Averaging`,
- `Fisher Merging`,
- `RegMean`,
- `Task Arithmetic`,
- `TIES-Merging`,
- `DARE`,
- `Consensus Merging`,
- `PCB Merging`.

Ako silné, ale menej praktické alternatívy uvádzajú:

- `AdaMerging`,
- `Representation Surgery`,
- `EMR-Merging`,
- `Twin-Merging`.

Ich pozícia je, že data-free prístupy sú prakticky atraktívne, ale stále zaostávajú za test-time adaptation metódami. Cieľom WUDI je tento rozdiel zmenšiť bez obetovania data-free charakteru.

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je navrhnúť data-free merge metódu, ktorá minimalizuje interferenciu medzi expert modelmi priamo v priestore task vectorov a dosiahne výkon porovnateľný alebo lepší než doterajšie silné baseline.

Hlavnú výskumnú otázku možno formulovať takto:

**Dá sa interferencia pri model merging-u odstrániť bez akýchkoľvek dodatočných dát, ak využijeme geometrickú štruktúru task vectorov v lineárnych vrstvách?**

S tým súvisia aj podotázky:

- či task vectors lineárnych vrstiev skutočne nesú informáciu o zodpovedajúcom vstupe,
- ako túto štruktúru využiť pri data-free merge,
- či takýto prístup prekoná bežné data-free baseline aj silné TTA merge metódy,
- a či bude fungovať aj pri LoRA fine-tuned modeloch.

## 4) Koncepčný rámec článku

Koncepčný rámec článku stojí na tvrdení, že `task vectors` lineárnych vrstiev tvoria približný lineárny podpriestor zodpovedajúceho vstupu. Autori vychádzajú z pozorovania, že pri jemnom fine-tuningu sú vstupy do lineárnych vrstiev počas optimalizácie relatívne konzistentné, takže kumulatívny weight update možno aproximovať ako kombináciu fixných vstupných smerov.

Na tomto základe definujú:

- `task vector` pre expert model,
- `merged task vector`,
- a `interference vector` ako rozdiel medzi merged a expert task vectorom.

Interferencia je potom formulovaná ako chyba medzi tým, ako merged a expert task vector pôsobia na zodpovedajúci input. To vedie k optimalizačnému cieľu, ktorý minimalizuje odchýlku merged task vectoru od expert task vectorov v lineárnych vrstvách.

Koncepčne je dôležité aj to, že autori sa sústredia najmä na `linear layers`, pretože práve tam task vectors nesú väčšinu schopností expert modelov a zároveň sa ich teória najlepšie aplikuje.

## 5) Použitá metóda (stratégia) výskumu

Ide o `teoreticko-empirický` článok.

Najprv autori teoreticky odvádzajú hornú hranicu interferencie a ukazujú, že jej minimalizácia vedie k praktickému cieľu definovanému len cez task vectors. Následne navrhujú `WUDI-Merging`, kde:

- sa task vectors expertov získajú ako rozdiel od pretrained modelu,
- merged task vector sa inicializuje súčtom expert task vectorov,
- pre každú lineárnu vrstvu sa optimalizuje samostatný loss,
- optimalizácia sa robí gradient descentom bez potreby hľadať ručne regularizačný koeficient,
- po vrstve sa zloží finálny merged task vector a pripočíta k pretrained modelu.

Praktická výhoda metódy je, že:

- nepotrebuje žiadne dodatočné dáta,
- nepotrebuje test-time samples,
- nepotrebuje rescaling koeficienty typu `lambda`,
- a rieši sa layer-by-layer s nízkou výpočtovou cenou.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Autori vyhodnocujú metódu na troch typoch úloh:

- `vision tasks`,
- `discriminative language tasks`,
- `generative language tasks`.

Pre vision používajú 8-task benchmark s modelmi:

- `ViT-B/32`,
- `ViT-B/16`,
- `ViT-L/14`.

Pre diskriminačné jazykové úlohy používajú:

- `GLUE`,
- `RoBERTa-Base`,
- `RoBERTa-Large`.

Pre generatívne jazykové úlohy používajú:

- `Llama2`,
- expert modely typu `WizardLM`, `WizardMath`, `CodeAlpaca`,
- a hodnotenia ako `AlpacaEval`, `GSM8K`, `MATH`, `HumanEval`, `MBPP`.

Navyše dopĺňajú experimenty na `LoRA` fine-tuned modeloch:

- `Flan-T5-base`,
- `Qwen-14B`.

Tým pokrývajú vision, encoderové jazykové modely, decoderové LLM aj PEFT scenáre.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Analýza kombinuje:

- teoretickú deriváciu interference objective,
- benchmarkové porovnanie s početnými baseline,
- a ablačné štúdie.

Autori porovnávajú `WUDI-Merging` s:

- `Weight Averaging`,
- `Fisher Merging`,
- `RegMean`,
- `Task Arithmetic`,
- `TIES-Merging`,
- `Consensus Merging`,
- `PCB Merging`,
- `AdaMerging`,
- `Representation Surgery`.

Sledujú:

- priemernú multi-task accuracy alebo normalized score,
- rozdiel voči najsilnejšej data-free baseline,
- rozdiel voči test-time adaptation metódam,
- stabilitu voči počtu optimalizačných krokov,
- vplyv počtu taskov,
- a vplyv balanced weighting-u v objective.

Dôležité je, že metódu analyzujú aj z pohľadu efektivity: uvádzajú, že konvergencia typicky nastáva v `100-200` iteráciách a optimalizácia sa robí po vrstvách.

## Hlavné zistenia

Článok ukazuje, že data-free merge sa dá výrazne zlepšiť, ak sa interferencia rieši priamo cez štruktúru task vectorov lineárnych vrstiev.

Hlavné zistenia sú:

- `WUDI-Merging` dosahuje `state-of-the-art` výkon v data-free model merging-u,
- v priemere zlepšuje výsledky oproti baseline data-free metódam o približne `10.9%`,
- zároveň prekonáva aj mainstream `test-time adaptation` merge prístupy približne o `3.3%`,
- na `ViT-B/32` prekonáva dovtedajšiu najsilnejšiu data-free baseline `PCB-Merging` o `8.9%`,
- na `ViT-L/14` ju prekonáva o `5.1%`,
- na `RoBERTa-Base` a `RoBERTa-Large` dosahuje výrazný skok oproti `TIES-Merging`,
- pri generatívnych jazykových úlohách zlepšuje priemerné skóre oproti `TIES-Merging (w/ DARE)` o `4.3%`,
- aj pri `LoRA` fine-tuned modeloch ostáva najsilnejšou metódou, hoci zisky sú menšie než pri plne fine-tuned settingu,
- balanced weighting v objective zlepšuje generalizáciu,
- metóda konverguje rýchlo a nepotrebuje veľa optimalizačných krokov,
- pri code úlohách nie je výhoda taká výrazná, čo autori pripisujú silnému konfliktu medzi math a code taskami.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Článok je pre DP hodnotný najmä ako moderný a teoreticky podopretý prístup k `data-free model merging`.

Je relevantný preto, že:

- rieši weight-level knowledge combination bez akýchkoľvek dodatočných dát,
- explicitne sa zameriava na `interference`,
- používa `task vectors` ako nositeľov informácie o učení,
- prepája teóriu lineárnych vrstiev s praktickým merge algoritmom,
- a ukazuje, že data-free merge môže konkurovať aj metódam používajúcim test-time dáta.

Pre diplomovú prácu je to dobrý článok najmä pri porovnaní heuristických merge prístupov s novšími metódami, ktoré majú teoretické zdôvodnenie a zároveň ostávajú prakticky použiteľné.

## Dôkazové citácie / oporné časti článku

- Motivácia:
  - "parameter interference between constituent models frequently induces performance degradation"
  - "resolving interference without additional data for retraining or test-time computation remains challenging"

- Teoretická myšlienka:
  - "the task vectors of the linear layer constitute an approximate linear subspace for its corresponding input"
  - "we can minimize interference under the guidance of task vectors"

- Metóda:
  - "WUDI-Merging ... eliminates interference without any additional data or rescaling coefficients"
  - "we primarily focus on the linear layers of the model"

- Výsledky:
  - "achieving state-of-the-art performance in data-free model merging scenarios"
  - "average 10.9% improvement versus baseline methods"
  - "outperforming mainstream test-time adaptation approaches by 3.3%"

- Praktická efektivita:
  - "convergence typically achieved within 100 to 200 iterations"
  - "only very few computing resources are required"

