# Model Merging in Pre-training of Large Language Models

## Fáza A: bibliografická karta

- Názov: Model Merging in Pre-training of Large Language Models
- Autori: Yunshui Li, Yiyuan Ma, Shen Yan, Chaoyi Zhang, Jing Liu, Jianqiao Lu, Minrui Wang, Mengzhao Chen, Xunhao Lai, Jin Ma, Shiyi Zhan, Deyi Liu, Yao Luo, Xingyan Bin, Ziwen Xu, Mingji Han, Wenhao Hao, Bairen Yi, Lingjun Liu, Bole Ma, Hongbin Ren, Xiaoying Jia, Xun Zhou, Siyuan Qiao, Liang Xiang, Yonghui Wu
- Rok: 2025
- Typ článku: NeurIPS 2025 poster + arXiv
- Link: <https://arxiv.org/abs/2505.12082>
- Alternatívny link: <https://openreview.net/forum?id=HW55AwGEC8>
- Téma: Model merging / model soups
- Relevancia pre DP: `4/5`

## 1) Motivácia autorov výskumu

Autori vychádzajú z toho, že veľké jazykové modely sú veľmi nákladné na pre-training, ich výkon počas tréningu sa ťažko predikuje a samotný tréning býva nestabilný. Model merging sa už ukázal ako sľubný v post-training fáze, no jeho použitie priamo počas pre-trainingu zostáva málo preskúmané.

Motiváciou článku je preto overiť, či checkpoint merging počas pre-training trajektórie dokáže:

- zlepšiť výkon bez ďalších veľkých nákladov,
- rýchlejšie simulovať efekt annealingu,
- znížiť tréningové náklady,
- pomôcť stabilizovať ďalšie tréningové fázy alebo obnoviť tréning po loss spike.

## 2) Lokalizácia analýzy literatúry

Analýza súvisiacej literatúry sa nachádza najmä v sekcii `2 Related Work`.

Autori tu odlišujú dve hlavné línie:

- `post-training model merging`, kde sa kombinujú task-specific modely po fine-tuningu,
- `pre-training checkpoint merging`, kde sa kombinujú checkpointy z jednej tréningovej trajektórie.

Ako relevantné predchádzajúce práce spomínajú napríklad Task Arithmetic, TIES-Merging, AdaMerging, Fisher Merging, RegMean, Evolutionary-model-merge a DARE. Priamo pre pre-training fázu odkazujú na práce typu `LAWA`, `Checkpoint Merging` a na technické reporty ako DeepSeek a LLaMA 3.1, kde sa merging počas pre-trainingu spomína, ale nebýva detailne popísaný.

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je systematicky preskúmať, ako funguje model merging počas pre-trainingu veľkých jazykových modelov, a navrhnúť praktický rámec `PMA` (`Pre-trained Model Average`) pre checkpoint averaging v tejto fáze.

Hlavnú výskumnú otázku možno formulovať takto:

**Môže merging checkpointov počas pre-trainingu veľkých jazykových modelov konzistentne zlepšovať výkon, nahrádzať alebo simulovať annealing a zároveň slúžiť ako praktický nástroj na stabilizáciu ďalšieho tréningu?**

Autori ju rozbíjajú aj na konkrétne podotázky:

- aký je vplyv merging na výkon počas stabilnej a annealing fázy,
- ktoré merging stratégie fungujú najlepšie,
- ako voľba intervalu checkpointov a počtu mergovaných modelov závisí od veľkosti modelu,
- či merged checkpointy pomáhajú pri ďalšom `CT` a `SFT`,
- či pomáhajú pri tréningovej stabilite a pri zotavení z loss spike.

## 4) Koncepčný rámec článku

Koncepčný rámec článku je založený na myšlienke, že checkpointy z jednej pre-training trajektórie predstavujú rôzne, ale príbuzné body v loss landscape. Ich priemerovanie môže využiť komplementárnosť medzi týmito bodmi a posunúť výsledný model do lepšej oblasti priestoru parametrov.

Autori zavádzajú dve hlavné pojmové línie:

- `PMA` ako checkpoint-level weight merging počas pre-trainingu,
- `PMA-init` ako použitie merged checkpointov na inicializáciu ďalšieho continued training alebo supervised fine-tuningu.

Článok je zároveň zasadený do rámca `Warmup-Stable-Decay (WSD)` learning rate schedule. Dôležitý koncept je, že merging checkpointov zo stabilnej fázy môže fungovať ako lacný simulátor neskoršieho annealed výkonu. V mechanistickej časti autori prepájajú účinok merge s druhoradovou aproximáciou lossu a s geometriou Hessianu.

## 5) Použitá metóda (stratégia) výskumu

Ide o rozsiahly `experimentálny kvantitatívny výskum` kombinovaný s abláciami a čiastočnou teoretickou analýzou.

Autori navrhujú stratégiu `PMA`, teda averaging checkpointov počas pre-trainingu, a následne ju testujú na rôznych architektúrach a škálach modelov. Skúmajú viacero merge variantov:

- `SMA` (Simple Moving Average),
- `WMA` (Weighted Moving Average),
- `EMA` (Exponential Moving Average).

Metodika výskumu pokrýva šesť otázok:

- vplyv merging na výkon,
- porovnanie merge metód,
- voľbu optimálneho intervalu medzi checkpointmi a počtu checkpointov,
- vplyv merged checkpointov na downstream training,
- vplyv na tréningovú stabilitu,
- mechanizmy, prečo merging funguje.

Okrem hlavných experimentov autori robia aj teoretickú analýzu cez Taylorovu aproximáciu lossu a vizualizácie parametrov v priestore váh.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Dáta vznikajú priamo počas tréningu série dense a `MoE` modelov od miliónových škál až po modely s viac ako `100B` parametrami. Autori uvádzajú, že modely trénovali od začiatku a používali interný pretraining corpus s `trillion-scale` objemom tokenov, hoci detailná špecifikácia datasetov a architektúr nie je plne zverejnená.

Vyhodnotenie prebieha na otvorených benchmarkoch, pričom článok explicitne spomína napríklad:

- `HumanEval`,
- `BBH`,
- `MMLU`,
- `GSM8K`,
- a širší `Open-Benchmark`, ktorý zahŕňa aj ďalšie úlohy ako `DROP`, `WinoGrande`, `HellaSwag`, `C-Eval`, `TriviaQA`, `MATH`, `MBPP`, `AGIEval`, `GPQA` či `MMLU-Pro`.

Autori používajú aj `in-house` eval set a sledujú weighted average score naprieč benchmarkmi ako súhrnnú metriku celkového výkonu. Pri experimentoch s downstream trainingom zbierajú aj loss curves, GradNorm priebehy a porovnania po continued training (`CT`) a supervised fine-tuningu (`SFT`).

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Analýza dát je prevažne `kvantitatívna komparatívna analýza`.

Autori porovnávajú:

- baseline checkpointy vs merged checkpointy,
- výsledky počas stabilnej fázy vs annealing fázy,
- rôzne merge schémy (`SMA`, `WMA`, `EMA`),
- rôzne intervaly checkpointov a rôzny počet checkpointov,
- baseline inicializáciu vs `PMA-init` pre `CT` a `SFT`,
- pôvodnú nestabilnú tréningovú trajektóriu vs resumed training s `PMA-init`.

Výsledky hodnotia cez:

- benchmarkové skóre na viacerých úlohách,
- weighted average score naprieč benchmarkmi,
- loss curves,
- GradNorm stabilitu,
- a v mechanistickej časti aj cez teoretickú analýzu straty pomocou Hessianu a vizualizáciu kontúr výkonu v priestore váh.

Táto analytická stratégia je dôležitá, lebo článok neposudzuje merging iba ako jednorazové zlepšenie skóre, ale aj ako nástroj na monitorovanie, predikciu a stabilizáciu pre-training procesu.

## Hlavné zistenia

Článok ukazuje, že merging checkpointov počas pre-trainingu môže byť prakticky veľmi užitočný.

Hlavné zistenia sú:

- checkpoint merging v stabilnej fáze tréningu prináša konzistentné zlepšenia výkonu naprieč veľkosťami modelov,
- `PMA` použitý v skoršej časti annealing fázy často dosahuje porovnateľný alebo lepší výkon než neskoré annealed checkpointy,
- kombinácia `constant learning rate + PMA` môže prakticky nahrádzať priebežné annealing validácie a tým šetriť výpočtové náklady,
- rozdiely medzi `SMA`, `WMA` a `EMA` sa neskôr zmenšujú, hoci v skorších fázach môže mať miernu výhodu `WMA`,
- optimálny interval medzi checkpointmi škáluje s veľkosťou modelu,
- väčší počet mergovaných checkpointov po dokončení tréningu zvyčajne vedie k lepšiemu výkonu,
- `PMA-init` pomáha so stabilitou continued trainingu a v niektorých situáciách umožňuje zotavenie z irrecoverable loss spike bez retrainingu od nuly,
- mechanistická analýza naznačuje, že merging ťaží z komplementarity checkpointov v loss landscape.

Pre DP je podstatné najmä to, že článok rozširuje merge perspektívu z post-training kombinácie znalostí na `checkpoint-level fusion počas pre-trainingu`.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Článok je pre DP veľmi užitočný, aj keď je trochu odlišný od klasických prác o kombinovaní task-specific expertov po fine-tuningu.

Jeho prínos pre tému `Kombinácia znalostí veľkých jazykových modelov` spočíva v tom, že:

- ukazuje, že merge nemusí byť len post-training technika, ale aj nástroj v samotnom pre-training procese,
- poskytuje praktické poznatky o checkpoint averagingu, intervaloch a počte checkpointov,
- prepája merge s témami ako tréningová stabilita, simulácia annealingu a lacnejšie model development cykly,
- rozširuje tvoje teoretické pokrytie merge prístupov smerom k infra/training perspektíve.

Pre DP je tento článok vhodný najmä ako rozširujúci a kontextový zdroj. Nie je centrálne o zlučovaní rôznych expert knowledge sources v zmysle task-specialized LLMs, ale výborne ukazuje, že kombinovanie znalostí vo weight space má širšie využitie než len fine-tuning merge.

## Dôkazové citácie / oporné časti článku

- Motivácia:
  - "its application in large-scale pre-training remains relatively unexplored"
  - "LLMs still face several critical challenges, including the extensive pre-training costs ... imprecisely-predictable performance scaling, as well as the instability of large-scale training"

- Hlavná metóda:
  - "we mainly focus on model merging during the pre-training stage, introducing Pre-trained Model Average (PMA)"
  - "PMA-init"

- Hlavné výsledky:
  - "model merging during the stable training phase yields consistent performance gains"
  - "applying PMA at early-stage of the cosine-decay phase usually achieve comparable or even superior performance to their final-stage annealing counterparts"
  - "constant learning rate, combined with model merging, can effectively match the performance of an annealed model"

- Downstream a stabilita:
  - "entering CT and SFT stages with PMA applied could yield smoother GradNorm curves"
  - "applying PMA-init over N preceding checkpoints to resume training, enables reliable recovery from unstable training trajectories"

- Mechanistická interpretácia:
  - "the effectiveness of model weight merging stems from the fact that different model checkpoints ... have explored different local regions or directions within the parameter space"
  - "the averaging process can smooth out idiosyncrasies of individual checkpoints"

