# When is Task Vector Provably Effective for Model Editing? A Generalization Analysis of Nonlinear Transformers

## Fáza A: bibliografická karta

- Názov: When is Task Vector Provably Effective for Model Editing? A Generalization Analysis of Nonlinear Transformers
- Autori: Hongkang Li, Yihua Zhang, Shuai Zhang, Meng Wang, Sijia Liu, Pin-Yu Chen
- Rok: 2025
- Typ článku: ICLR 2025 oral + arXiv
- Link: <https://arxiv.org/abs/2504.10957>
- Alternatívny link: <https://openreview.net/forum?id=vRvVVb0NAz>
- Téma: Task vectors / editing in weight space
- Relevancia pre DP: `4/5`

## 1) Motivácia autorov výskumu

Autori vychádzajú z toho, že `task vectors` sa empiricky osvedčili pri multi-task learningu, forgettingu aj out-of-domain generalization, no stále chýba teoretické vysvetlenie, prečo tieto operácie fungujú. Najmä pri Transformeroch je problémom vysoká nelinearita a neconvexita tréningu, kvôli čomu sa doterajšie analýzy ťažko prenášajú na realistické modely.

Motiváciou článku je preto odpovedať na otázku, `kedy a prečo` môže task-vector prístup fungovať spoľahlivo. Autori chcú dať nielen intuitívne, ale aj formálne podmienky, za ktorých bude task addition, task negation a out-of-domain generalization úspešná.

## 2) Lokalizácia analýzy literatúry

Analýza súvisiacej literatúry je sústredená najmä v sekcii `1.2 Related Works`.

Autori ju delia na tri hlavné okruhy:

- `weight interpolation / model merging`,
- teoretické analýzy `Transformers` vo feature-learning rámci,
- teoretické štúdie iných `PEFT` metód.

Voči predchádzajúcim prácam sa článok pozicionuje tak, že:

- doterajšie task-vector práce boli prevažne empirické,
- NTK-based vysvetlenia sa opierali o linearized modely a neposkytovali plné generalization guarantees pre nelineárne Transformery,
- teoretické práce o unlearningu sa zvyčajne netýkali Transformer-based modelov ani širších operácií ako multi-task learning či out-of-domain generalization.

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je teoreticky charakterizovať, za akých podmienok je task vector prístup efektívny pri model editingu na nelineárnych Transformeroch.

Hlavnú výskumnú otázku možno formulovať takto:

**Kedy a prečo môže task vector prístup úspešne a efektívne realizovať multi-task learning, unlearning a out-of-domain generalization na nelineárnych Transformer modeloch?**

Autori ju rozkladajú na tri konkrétne problémy:

- ako korelácia medzi úlohami ovplyvňuje úspech `task addition` a `task negation`,
- ako voliť lineárne koeficienty `lambda_i` pre požadované generalizačné správanie,
- či low-rank a sparse aproximácie task vectorov zachovávajú tieto záruky.

## 4) Koncepčný rámec článku

Koncepčný rámec článku spája `task arithmetic` s teóriou generalizácie pre `nonlinear Transformers`.

Autori definujú task vector klasicky ako rozdiel medzi:

- pre-trained modelom `Psi^(0)`,
- a modelom fine-tuned na úlohe `Psi*_T`.

Zásadným rozšírením je, že neanalyzujú task arithmetic len ako heuristiku, ale ako generalizačný mechanizmus závislý od:

- `korelácie medzi taskami`,
- `výberu aritmetických koeficientov`,
- `štruktúry learned features`,
- a `redundancie` task vectorov.

Rozlišujú tri typy vzťahov medzi úlohami:

- `irrelevant`,
- `aligned`,
- `contradictory`.

Práve typ tejto korelácie určuje, či je vhodná adícia alebo negácia task vectora. Zároveň autori prepájajú task arithmetic s efektívnymi aproximáciami, teda `low-rank` a `sparse` task vectors.

## 5) Použitá metóda (stratégia) výskumu

Ide o `teoreticko-empirický výskum`, kde hlavnú časť tvorí formálna analýza a menšiu časť experimentálne overenie.

Teoretická stratégia je postavená na:

- zjednodušenom koncepčnom nastavení binárnej klasifikácie,
- feature-learning analýze `one-layer nonlinear Transformer`,
- odvodení sufficient conditions pre úspešné `addition`, `negation` a `out-of-domain generalization`,
- a dôkazoch pre low-rank a sparse aproximácie task vectorov.

Kľúčové výsledky sú formulované cez:

- `Theorem 1` pre multi-task learning,
- `Theorem 2` pre unlearning,
- `Theorem 3` pre out-of-domain generalization,
- `Corollary 1` a `Corollary 2` pre low-rank a sparsity.

Empirická časť potom slúži ako validácia týchto tvrdení na praktických modeloch a datasetoch.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Článok pracuje s dvoma úrovňami dát:

- `synteticko-koncepčná` úroveň pre teoretické a kontrolované experimenty,
- `praktická` úroveň pre validáciu na jazykových modeloch.

Pre obrazové experimenty autori používajú:

- `Colored-MNIST`,
- `ViT-Small/16` pre-trained z `ImageNet-21K`,
- binárne parity klasifikačné úlohy s kontrolovanou koreláciou medzi farbou a triedou, aby vytvorili `irrelevant`, `aligned` a `contradictory` task pairs.

Pre jazykovú generáciu používajú:

- `Phi-1.5 (1.3B)`,
- datasety `Harry Potter 1`, `Harry Potter 2`, `Pride and Prejudice`,
- metriku `Rouge-L` na meranie miery unlearningu a zachovania generatívnych schopností.

Dodatočne overujú low-rank verziu task vectorov cez:

- `LoRA`,
- a v dodatkoch aj `Phi-3-small (7B)`.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Autori kombinujú `formálnu matematickú analýzu` s `kvantitatívnym experimentálnym porovnaním`.

Na teoretickej úrovni analyzujú:

- generalization error po aplikácii task vectors,
- vzťah medzi koreláciou úloh a správnym znamienkom / veľkosťou koeficientov `lambda`,
- podmienky, za ktorých merged model generalizuje na novú úlohu,
- a robustnosť týchto tvrdení voči low-rank a sparse aproximácii.

Na experimentálnej úrovni sledujú:

- accuracy pri multi-task learningu a unlearningu na `Colored-MNIST`,
- heatmapy výkonu pre rôzne dvojice koeficientov `lambda_1, lambda_2`,
- `Rouge-L` pri unlearningu textových datasetov,
- porovnanie full-rank a low-rank task vectorov.

Silnou stránkou metodiky je, že empirické grafy a tabuľky nie sú len ilustratívne, ale priamo slúžia ako validácia teoretických tvrdení.

## Hlavné zistenia

Článok prináša prvé silnejšie teoretické záruky pre task arithmetic na nelineárnych Transformeroch.

Hlavné zistenia sú:

- `task addition` je provably efektívna pri `irrelevant` alebo `aligned` taskoch,
- `task negation` je provably úspešná pri `irrelevant` alebo `contradictory` taskoch,
- správna voľba koeficientov `lambda_i` môže zabezpečiť `out-of-domain generalization` aj mimo jednoduchých analógii,
- korelácia medzi taskami je kľúčová premenná, ktorá určuje, či bude addition alebo negation úspešná,
- low-rank a sparse aproximácie task vectorov zachovávajú generalization guarantees do veľkej miery,
- empirické experimenty na `Colored-MNIST` presne reprodukujú teoretické predikcie pre aligned / contradictory / irrelevant režimy,
- pri unlearningu na `Phi-1.5` negatívny task vector na `HP1` znižuje výkon na aligned `HP2` výraznejšie než na menej aligned `PP`, čo podporuje teoretické tvrdenia,
- low-rank LoRA task vectors zachovávajú rovnaké kvalitatívne správanie pri nižšej pamäťovej náročnosti.

Najdôležitejšie pre DP je, že článok prekladá task-vector literatúru z empirickej intuície do formálnej analýzy podmienok úspechu.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Článok je pre DP veľmi vhodný ako `teoretický pilier` k task vectors.

Jeho prínos pre tému `Kombinácia znalostí veľkých jazykových modelov` spočíva v tom, že:

- vysvetľuje, prečo môže kombinovanie task vectors fungovať alebo zlyhávať,
- zavádza jazyk `irrelevant / aligned / contradictory tasks`, ktorý vieš použiť aj v analytickej časti DP,
- dáva formálnu oporu pre low-rank a sparse task vectors, čo je dôležité pri efektívnom merge/editingu,
- prepája multi-task learning, forgetting a out-of-domain transfer v jednom jednotnom teoretickom rámci.

Pre DP je tento článok cenný najmä na ukázanie nielen toho, `že` task vectors fungujú, ale aj `kedy` a `za akých podmienok` ich možno rozumne použiť.

## Dôkazové citácie / oporné časti článku

- Motivácia:
  - "theoretical understanding of why task vectors can execute various conceptual operations remains limited"
  - "When and why can the task vector approach perform well in multi-task learning, unlearning, and out-of-domain generalization successfully and efficiently?"

- Hlavný prínos:
  - "the first theoretical characterization of the generalization guarantees of task vector methods on nonlinear Transformers"
  - "the first provable out-of-domain generalization guarantees through task arithmetic"

- Podmienky úspechu:
  - "task addition is effective for multi-task learning when the tasks are either irrelevant or aligned"
  - "task negation is provably successful for unlearning tasks that are either irrelevant or contradictory"

- Efektivita:
  - "the generalization guarantees are minimally affected by these approximations"
  - "the validity of magnitude-based pruning on task vectors like TIES ... or DARE"

- Empirická validácia:
  - "This result verifies Theorems 1 and 2"
  - "This verifies Theorem 2 that unlearning a task THP1 can effectively degrade the performance of the aligned task"

