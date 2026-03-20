# Editing Models with Task Arithmetic

## Fáza A: bibliografická karta

- Názov: Editing Models with Task Arithmetic
- Autori: Gabriel Ilharco, Marco Tulio Ribeiro, Mitchell Wortsman, Suchin Gururangan, Ludwig Schmidt, Hannaneh Hajishirzi, Ali Farhadi
- Rok: 2022
- Typ článku: ICLR 2023 + arXiv
- Link: <https://arxiv.org/abs/2212.04089>
- Alternatívny link: <https://iclr.cc/virtual/2023/poster/12254>
- Téma: Task vectors / editing in weight space
- Relevancia pre DP: `5/5`

## 1) Motivácia autorov výskumu

Autori vychádzajú z praktickej potreby `editovať` pre-trained modely po pre-trainingu. Takéto úpravy môžu smerovať k zlepšeniu výkonu na downstream úlohách, k obmedzeniu nežiaducich správaní a biasov, k zosúladeniu modelu s preferenciami používateľov alebo k doplneniu nových znalostí.

Motiváciou článku je navrhnúť jednoduchý a všeobecný mechanizmus, ktorý by umožnil meniť správanie modelu priamo vo `weight space`, bez potreby vždy znovu trénovať celý model. Autori preto zavádzajú `task vectors` ako smer v priestore váh, ktorý zachytáva informáciu získanú fine-tuningom na konkrétnej úlohe.

## 2) Lokalizácia analýzy literatúry

Analýza súvisiacej literatúry je najviac sústredená v sekcii `7 Related work`, no relevantné odkazy sa objavujú aj v úvode a metodickom rámci.

Autori prepájajú najmä tieto línie literatúry:

- editing modelov po pre-trainingu,
- weight interpolation a weight averaging,
- multi-task parameter averaging,
- forgetting / unlearning,
- rozdiel medzi lineárnym a nelineárnym režimom neurónových sietí.

Článok sa tak nachádza na priesečníku medzi prácami o fine-tuningu, model merging a geometrickom pohľade na weight space.

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je overiť, či možno informáciu získanú fine-tuningom reprezentovať ako `task vector` a potom ju manipulovať jednoduchými aritmetickými operáciami tak, aby sa cielene menilo správanie modelu.

Hlavnú výskumnú otázku možno formulovať takto:

**Dá sa správanie pre-trained modelu účinne meniť pomocou aritmetiky nad task vectors, teda nad rozdielmi medzi fine-tuned a pre-trained váhami?**

S tým súvisia aj tri podotázky:

- či negácia task vectora umožní oslabovať alebo odstraňovať konkrétne schopnosti,
- či sčítanie task vectorov umožní kombinovať viaceré schopnosti do jedného modelu,
- či kombinácia task vectorov podľa analógie dokáže zlepšiť výkon na úlohe, pre ktorú máme málo alebo žiadne označené dáta.

## 4) Koncepčný rámec článku

Koncepčný rámec článku je postavený na predstave, že fine-tuning na úlohe zanecháva v priestore parametrov `smer`, ktorý nesie task-specific informáciu.

Formálne autori definujú task vector ako rozdiel:

- medzi váhami pre-trained modelu `theta_pre`,
- a váhami toho istého modelu po fine-tuningu na úlohe `theta_ft`.

Výsledný vektor `tau = theta_ft - theta_pre` sa potom dá aplikovať na kompatibilný model pomocou adície so škálovacím koeficientom `lambda`.

Na tomto základe článok rozlišuje tri základné operácie:

- `negation`, teda odčítanie/negovanie task vectora,
- `addition`, teda sčítanie viacerých task vectorov,
- `task analogies`, kde sa task vectors kombinujú vo forme `A je k B ako C je k D`.

Z interpretačného hľadiska autori zároveň predpokladajú, že úspech task arithmetic súvisí s určitým stupňom disentanglementu a približnej ortogonality medzi smermi rôznych úloh.

## 5) Použitá metóda (stratégia) výskumu

Ide o `experimentálny kvantitatívny výskum` s návrhom novej reprezentácie úloh vo weight space a s rozsiahlym empirickým testovaním.

Metodika je postavená na troch typoch experimentov:

- `forgetting via negation`, kde sa skúma, či negatívny task vector odstráni cieľové správanie bez poškodenia kontrolných úloh,
- `learning via addition`, kde sa sčítavajú task vectors s cieľom vytvoriť multi-task model alebo zlepšiť single-task výkon,
- `task analogies`, kde sa vektory kombinujú analogickým spôsobom na prenos znalostí do nových, dátovo chudobných úloh.

Pre každú operáciu autori aplikujú task vectors element-wise na váhy modelu a koeficient `lambda` vyberajú podľa validačných dát. V závere robia aj doplnkovú analýzu podobnosti task vectorov, lokalizácie eigenfunkcií a porovnanie s náhodnou inicializáciou, aby vysvetlili, prečo task arithmetic funguje.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Dáta pre vyhodnotenie pochádzajú z viacerých modelov, modalít a benchmarkov.

Pre obrazovú časť autori používajú najmä `CLIP` modely a osem image-classification úloh:

- `Cars`,
- `DTD`,
- `EuroSAT`,
- `GTSRB`,
- `MNIST`,
- `RESISC45`,
- `SUN397`,
- `SVHN`.

Ako kontrolnú úlohu pri negácii používajú `ImageNet`.

Pre textovú časť používajú:

- `GPT-2` pri experimentoch so znižovaním toxicity,
- `T5` modely pri NLP experimentoch,
- úlohy z `GLUE` ako `MRPC`, `RTE`, `CoLA`, `SST-2`,
- sentiment analýzu nad datasetmi `Amazon` a `Yelp`,
- a experimenty s málo dátovými subpopuláciami pre task analogies.

Task vectors sú získané z rozdielu medzi pre-trained a fine-tuned checkpointmi tých istých modelov. Výsledky sa zbierajú cez accuracy, perplexity, toxic generation rate a ďalšie task-specific metriky.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Autori používajú `kvantitatívnu komparatívnu analýzu`, pričom porovnávajú upravené modely s viacerými baseline prístupmi.

Pri negácii task vectorov porovnávajú:

- negatívny task vector,
- gradient ascent ako forgetting baseline,
- náhodný vektor rovnakej magnitúdy.

Pri sčítaní task vectorov sledujú:

- výkon výsledného jedného modelu na viacerých úlohách,
- porovnanie so zero-shot modelom,
- porovnanie s použitím viacerých špecializovaných fine-tuned modelov.

Pri task analogies sledujú, či kombinácia troch vektorov zlepší výkon na štvrtej úlohe bez potreby labeled target dát. V analytickej časti navyše skúmajú:

- cosine similarity medzi task vectors,
- približnú ortogonalitu medzi úlohami,
- lokalizáciu eigenfunkcií v NTK pohľade,
- a rozdiel medzi pre-trained a náhodne inicializovanými modelmi.

Táto kombinácia experimentálnej a interpretačnej analýzy robí z článku nielen metodický návrh, ale aj pokus o vysvetlenie, prečo task arithmetic funguje.

## Hlavné zistenia

Článok prináša niekoľko zásadných zistení:

- `negating` task vector účinne znižuje výkon na cieľovej úlohe alebo nežiaduce správanie, pričom len minimálne zasahuje kontrolné úlohy,
- pri CLIP modeloch negatívne task vectors výrazne znižujú target accuracy, ale len málo poškodzujú `ImageNet`,
- pri GPT-2 negatívny task vector znižuje toxic generations približne `6x` pri porovnateľnej perplexite,
- `adding` task vectors umožňuje vytvoriť jeden model kompetentný vo viacerých úlohách naraz,
- pri pároch image taskov dosahuje sčítanie task vectorov približne `98.9 %` normalizovanej accuracy oproti použitiu dvoch samostatných fine-tuned modelov,
- pri väčšom počte task vectorov sa dajú vytvoriť stále lepšie multi-task modely; pri všetkých ôsmich úlohách dosiahli približne `91.2 %` priemernej normalizovanej accuracy,
- pri T5 modeloch môžu externé task vectors zlepšiť výkon aj na jednej cieľovej NLP úlohe oproti samotnému fine-tuningu,
- task analogies dokážu zlepšiť výkon na cieľových úlohách aj bez labeled dát z target tasku,
- task vectors bývajú často približne ortogonálne a autori tvrdia, že task arithmetic je vlastnosť nadobudnutá počas pre-trainingu, nie všeobecná vlastnosť náhodne inicializovaných sietí.

Pre DP je najdôležitejšie, že tento článok zavádza task vectors ako explicitný mechanizmus kombinácie a manipulácie znalostí vo weight space.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Tento článok je pre DP kľúčový. Patrí medzi úplne základné zdroje k téme `kombinácie znalostí vo weight space`.

Jeho hodnota pre tému `Kombinácia znalostí veľkých jazykových modelov` je vysoká, pretože:

- zavádza pojem `task vector`, ktorý sa stal jedným z hlavných rámcov pre weight-space editing,
- ukazuje, že znalosti sa dajú nielen pridávať, ale aj odoberať a kombinovať,
- prepája model editing, model merging, unlearning a transfer v jednom jednotnom formálnom rámci,
- poskytuje aj empirické dôkazy na viacerých modeloch a modalitách,
- vytvára priamy most k neskorším prácam o tangent-space task arithmetic, layer-aware task arithmetic a teoretickým analýzam task vectors.

Ak by si mala v DP vybrať niekoľko úplne nosných článkov, tento by medzi nimi určite mal byť.

## Dôkazové citácie / oporné časti článku

- Definícia hlavnej myšlienky:
  - "A task vector specifies a direction in the weight space of a pre-trained model, such that movement in that direction improves performance on the task."
  - "We build task vectors by subtracting the weights of a pre-trained model from the weights of the same model after fine-tuning on a task."

- Aritmetika nad task vectors:
  - "these task vectors can be modified and combined together through arithmetic operations such as negation and addition"
  - "the behavior of the resulting model is steered accordingly"

- Negácia:
  - "Negating a task vector decreases performance on the target task, with little change in model behavior on control tasks."
  - "Negative task vectors reduce the accuracy of a pre-trained ViT-L/14 by 45.8 percentage points on the target tasks, with little loss on the control task."

- Sčítanie:
  - "Adding two task vectors maintains 98.9% of the accuracy"
  - "When all task vectors are available, the best model produced by adding task vectors reaches an average performance of 91.2%, despite compressing several models into one."

- Analógie:
  - "combining task vectors from three of the tasks can improve performance on the fourth, even when no data from the fourth task is used for training"
  - "Using task vectors outperforms fine-tuning on the remaining auxiliary sentiment analysis task"

- Interpretácia:
  - "vectors from different tasks are typically close to orthogonal"
  - "we conclude that task arithmetic is a property acquired during pre-training"

