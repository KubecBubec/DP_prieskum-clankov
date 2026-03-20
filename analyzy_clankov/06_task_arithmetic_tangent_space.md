# Task Arithmetic in the Tangent Space: Improved Editing of Pre-Trained Models

## Fáza A: bibliografická karta

- Názov: Task Arithmetic in the Tangent Space: Improved Editing of Pre-Trained Models
- Autori: Guillermo Ortiz-Jimenez, Alessandro Favero, Pascal Frossard
- Rok: 2023
- Typ článku: NeurIPS 2023 oral + arXiv
- Link: <https://arxiv.org/abs/2305.12827>
- Alternatívny link: <https://openreview.net/forum?id=0A9f2jZDGW>
- Téma: Task vectors / editing in weight space
- Relevancia pre DP: `4/5`

## 1) Motivácia autorov výskumu

Autori nadväzujú na článok `Editing Models with Task Arithmetic`, ktorý ukázal, že task vectors možno s úspechom sčitovať, negovať a kombinovať. Zároveň však upozorňujú, že pôvodný prístup stále funguje neúplne a jeho úspech nie je dobre teoreticky vysvetlený.

Motiváciou článku je preto pochopiť, prečo task arithmetic funguje a ako ho zlepšiť. Autori vychádzajú z hypotézy, že problémom klasického prístupu je nedostatočné `weight disentanglement` v nelineárne fine-tuned modeloch a že lepšie výsledky možno dosiahnuť, ak sa editing uskutoční v `tangent space` pre-trained modelu.

## 2) Lokalizácia analýzy literatúry

Súvisiaca literatúra je rozpracovaná najmä v sekcii `Related work`, ale jej vplyv je prítomný aj v celej argumentácii článku.

Autori prepájajú tri hlavné línie:

- práce o `task arithmetic` a weight-space editingu,
- literatúru o `NTK` a linearizovaných neurónových sieťach,
- práce o disentanglemente a geometrii reprezentácií počas pre-trainingu.

Článok teda rozširuje pôvodný task-vector rámec o teoretickejší pohľad: namiesto čisto empirického sčítania task vectors skúma, aký význam má lineárna aproximácia modelu v okolí pre-trained checkpointu.

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je zlepšiť task arithmetic tým, že sa vykoná v tangent space pre-trained modelu, a zároveň vysvetliť, prečo je takýto prístup účinnejší.

Hlavnú výskumnú otázku možno formulovať takto:

**Je možné zvýšiť účinnosť task arithmetic tým, že task vectors budú odvodené alebo aplikované v tangent space pre-trained modelu namiesto pôvodného nelineárneho weight space?**

S tým súvisia aj podotázky:

- či linearized fine-tuning vedie k lepšiemu weight disentanglementu,
- či sa tým zlepší negácia a sčítanie task vectorov,
- či tento efekt súvisí s lokalizáciou NTK eigenfunkcií,
- či je task arithmetic vlastnosťou získanou počas pre-trainingu.

## 4) Koncepčný rámec článku

Koncepčný rámec článku stojí na spojení `task arithmetic` a `tangent-space / NTK` pohľadu na model.

Autori rozlišujú tri relevantné objekty:

- `non-linear fine-tuning`, teda bežný fine-tuning v pôvodnom modeli,
- `post-hoc linearization`, teda linearizáciu už nelineárne fine-tuned modelu,
- `linearized fine-tuning`, teda fine-tuning priamo v tangent space pre-trained modelu.

Kľúčová idea je, že task arithmetic funguje lepšie vtedy, keď sú váhové zmeny pre jednotlivé úlohy viac `disentangled`, teda keď sa menej rušia navzájom. Autori tvrdia, že tangent space zvyšuje práve túto disentangláciu. Zároveň prepájajú úspech task arithmetic s lokalizáciou eigenfunkcií `NTK`, ktoré majú reprezentovať rôzne úlohy na odlišných oblastiach vstupného priestoru.

## 5) Použitá metóda (stratégia) výskumu

Ide o `experimentálny kvantitatívny výskum` doplnený teoretickou analýzou.

Autori porovnávajú klasické task vectors z nelineárne fine-tuned modelov s task vectors odvodenými z tangent-space prístupov. V praxi testujú:

- task arithmetic s `non-linear fine-tuning`,
- task arithmetic po `post-hoc linearization`,
- task arithmetic pri `linearized fine-tuning`.

Hodnotia najmä:

- `task addition`,
- zlepšenie multi-task výkonu,
- mieru `weight disentanglement`,
- vplyv veľkosti modelu,
- generalizáciu na iné architektúry.

Metodika teda nie je len porovnanie dvoch techník, ale systematické sledovanie toho, ako sa mení úspech task arithmetic v závislosti od linearizácie modelu.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Hlavné experimenty prebiehajú najmä na `CLIP` modeloch a rovnakom image benchmarku ako pri pôvodnom článku o task vectors. Používajú najmä osem úloh:

- `Cars`,
- `DTD`,
- `EuroSAT`,
- `GTSRB`,
- `MNIST`,
- `RESISC45`,
- `SUN397`,
- `SVHN`.

Okrem toho robia doplnkové experimenty na:

- `ConvNeXt` architektúre,
- `closed-vocabulary` modeli `ViT-B/16` pre-trained na `ImageNet-1k`,
- textových modeloch typu `T5`,
- a modeloch s náhodnou inicializáciou.

Výsledky zbierajú cez absolute a normalized accuracy pri task addition, cez fine-tuning accuracy, a cez špecifické metriky odvodené z `disentanglement error` a z lokalizácie NTK eigenfunkcií.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Autori používajú `kvantitatívnu komparatívnu analýzu` a zároveň teoreticko-interpretačnú analýzu.

Porovnávajú:

- nelineárne fine-tuned modely,
- post-hoc linearizované modely,
- modely fine-tuned priamo v tangent space.

Na experimentálnej úrovni sledujú:

- absolute accuracy,
- normalized accuracy,
- single-task vs multi-task výkon,
- rozdiely naprieč veľkosťami modelov,
- rozdiely medzi pre-trained a random-init modelmi.

Na interpretačnej úrovni analyzujú:

- `disentanglement error` v priestore task vectors,
- vizualizácie regiónov, kde je weight disentanglement silnejší,
- lokalizáciu `NTK` eigenfunkcií,
- a vzťah medzi scale modelu, kernel behavior a kvalitou task arithmetic.

Výsledkom je silná kombinácia empirického benchmarkingu a mechanistického vysvetlenia.

## Hlavné zistenia

Článok ukazuje, že task arithmetic možno systematicky zlepšiť, ak sa presunie do tangent space.

Hlavné zistenia sú:

- `linearized fine-tuning` vo všeobecnosti vedie k lepšiemu task arithmetic než bežný nelineárny fine-tuning,
- tangent-space prístup zvyšuje `weight disentanglement`, a tým znižuje interferenciu medzi task vectors,
- autori uvádzajú zlepšenia až približne `o 5.8 bodu` pri task addition a `o 13.1 bodu` menšiu degradáciu pri task negation,
- lineárne alebo post-hoc linearizované modely často dosahujú vyššiu accuracy pri task addition než nelineárne modely,
- rozdiel medzi lineárnym a nelineárnym prístupom sa zmenšuje s rastúcou veľkosťou modelu, pretože väčšie modely sa správajú viac kernelovo,
- random-initialized modely nedokážu robiť task arithmetic dobre, čo podporuje tézu, že ide o vlastnosť získanú počas pre-trainingu,
- podobné trendy sa objavujú aj mimo pôvodného CLIP setupu, vrátane CNN, T5 a closed-vocabulary modelov.

Najdôležitejšie je, že autori presúvajú diskusiu od otázky `či task arithmetic funguje` k otázke `za akých geometrických podmienok funguje lepšie`.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Článok je pre DP veľmi užitočný ako druhý krok po foundational článku `Editing Models with Task Arithmetic`.

Jeho hodnota spočíva v tom, že:

- prehlbuje task-vector rámec teoreticky aj empiricky,
- ukazuje, že quality merging/editing vo weight space závisí od geometrie reprezentácie,
- zavádza dôležitý pojem `weight disentanglement`,
- prepája task arithmetic s `NTK` a tangent-space interpretáciou,
- pomáha ti posunúť analýzu z úrovne empirických trikov na úroveň mechanizmov.

Pre DP je to veľmi vhodný článok najmä na ukázanie, že kombinácia znalostí vo weight space nie je len heuristika, ale jav, ktorý sa dá analyzovať cez lokálnu geometriu modelu.

## Dôkazové citácie / oporné časti článku

- Hlavná téza:
  - "weight disentanglement is the crucial factor enabling task arithmetic effectiveness"
  - "fine-tuning models in their tangent space by linearizing them amplifies weight disentanglement"

- Metóda:
  - "linearized fine-tuning"
  - "post-hoc linearization"
  - "the tangent space of the pre-trained model"

- Hlavné výsledky:
  - "linearized task arithmetic achieves the highest accuracies"
  - "linearized models exhibit significantly more weight disentanglement compared to their non-linear counterparts"

- Vzťah k pre-trainingu:
  - "weight disentanglement and, consequently, task arithmetic are emergent properties of pre-training"
  - "we conclude that task arithmetic is a property acquired during pre-training"

- Vzťah k mierke modelu:
  - "the gap between non-linear, linear, and post-hoc linearized models diminishes as the model size increases"
  - "larger models ... inherently induce a stronger kernel behavior during fine-tuning"

