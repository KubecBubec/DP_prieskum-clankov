# Model Merging by Uncertainty-Based Gradient Matching

## Fáza A: bibliografická karta

- Názov: Model Merging by Uncertainty-Based Gradient Matching
- Autori: Nico Daheim, Thomas Mollenhoff, Edoardo M. Ponti, Iryna Gurevych, Mohammad Emtiyaz Khan
- Rok: 2024
- Typ článku: ICLR 2024 poster + arXiv
- Link: <https://arxiv.org/abs/2310.12808>
- Alternatívny link: <https://openreview.net/forum?id=D7KJmfEDQP>
- Téma: Combining fine-tuned checkpoints
- Relevancia pre DP: `4/5`

## 1) Motivácia autorov výskumu

Autori vychádzajú z toho, že parameter averaging a task arithmetic sa v praxi často používajú úspešne, no stále nie je jasné, prečo fungujú a kedy zlyhávajú. Jednoduché vážené priemerovanie checkpointov sa javí ako prekvapivo silné pri prenose znalostí, odstraňovaní toxicity či adaptácii na nové úlohy, ale jeho presnosť a limity nie sú dobre vysvetlené.

Motiváciou článku je preto pochopiť chybu vznikajúcu pri merge a navrhnúť lepší weighting mechanizmus. Autori tvrdia, že rozhodujúci faktor je `gradient mismatch` medzi merge modelom a želaným target modelom a že jeho zníženie môže systematicky zlepšiť výsledok merging-u.

## 2) Lokalizácia analýzy literatúry

Analýza súvisiacej literatúry je rozptýlená medzi úvod a sekcie `2 Model Merging by Parameter Averaging` a `3.2.1 Relationship to Existing Methods`.

Autori prepájajú najmä tieto línie:

- `parameter averaging` a `weighted averaging`,
- `task arithmetic`,
- `Fisher-weighted averaging`,
- `TIES-merging`,
- a širší bayesovský / Laplace pohľad na uncertainty.

Článok sa voči nim nevymedzuje len empiricky, ale analyticky ukazuje, aké implicitné predpoklady tieto metódy robia o škálovaní, dvojitom započítaní informácie z base modelu a o tom, čo sa ignoruje pri aproximácii target modelu.

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je teoreticky vysvetliť chybu parameter averaging prístupov a navrhnúť merge metódu, ktorá túto chybu znižuje pomocou uncertainty-aware gradient matching.

Hlavnú výskumnú otázku možno formulovať takto:

**Ako súvisí chyba merged modelu s gradient mismatch medzi individuálne fine-tuned modelmi a target modelom, a dá sa merge zlepšiť tak, že sa tento mismatch explicitne zníži?**

S tým súvisia aj podotázky:

- prečo sa jednoduché averaging, task arithmetic a Fisher averaging správajú odlišne,
- ako zvoliť scaling matice alebo váhy pri merge,
- či sa dá merge formulovať cez druhoradovú aproximáciu a uncertainty,
- či takýto prístup pomôže pri task addition aj data removal.

## 4) Koncepčný rámec článku

Koncepčný rámec článku stojí na pojme `target model`, teda modeli, ktorý by vznikol spoločným tréningom na všetkých relevantných dátach a ku ktorému sa merge snaží priblížiť.

Autori ukazujú, že rozdiel medzi:

- target modelom,
- a merged modelom vytvoreným averagingom,

možno chápať cez `gradient mismatch`. Tento mismatch následne aproximujú druhým rádom pomocou `Hessian` alebo `Fisher` informácie.

Na tomto základe navrhujú `uncertainty-based gradient matching`, kde škálovanie task vectorov a parametrov závisí od odhadov curvature / uncertainty. Článok zároveň ukazuje, že:

- bežné averaging schémy možno chápať ako hrubé aproximácie,
- Fisher weighting je špeciálny prípad s implicitnými predpokladmi,
- a task arithmetic možno rozšíriť o lepšie preconditioners odvodené z Hessian/Fisher informácie.

## 5) Použitá metóda (stratégia) výskumu

Ide o `teoreticko-empirický výskum`.

Najprv autori analyticky odvádzajú vzťah medzi merge chybou a gradient mismatch. Následne z neho pomocou Taylorovej aproximácie odvodia novú merge schému, ktorá redukuje mismatch cez `second-order` informáciu.

Prakticky ich metóda:

- používa Hessian-like preconditioners,
- v experimentoch ich aproximuje cez diagonálny `Fisher`,
- a tým vytvára uncertainty-aware scaling pre task arithmetic / parameter averaging.

Metóda je následne porovnávaná s:

- jednoduchým averagingom,
- `task arithmetic`,
- `Fisher averaging`,
- `TIES`,
- `RegMean`.

Autori navyše navrhujú aj variant pre `data removal`, teda odstraňovanie nežiaducich schopností zo zlučovaného modelu.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Autori vyhodnocujú metódu na viacerých typoch modelov a úloh:

- `ViT` pre image classification,
- `RoBERTa` pre sentiment classification,
- `GPT-2`, `GPT-J` a `flanT5` pre language generation a data removal,
- a v dodatkoch aj `T5`.

Pre vision task addition používajú osem datasetov:

- `Cars`,
- `DTD`,
- `EuroSAT`,
- `GTSRB`,
- `MNIST`,
- `RESISC45`,
- `SUN397`,
- `SVHN`.

Pre NLP task addition používajú:

- `IMDB`,
- `Yelp`,
- `RottenTomatoes`,
- `SST2`,
- `Amazon`.

Pre data removal používajú dva scenáre:

- odstraňovanie halucinácií z dialogových modelov (`Wizard-of-Wikipedia` + syntetické halucinačné dáta),
- odstraňovanie toxicity pomocou `Civil Comments` a hodnotenie cez `RealToxicityPrompts`, `Detoxify`, `WikiText103`, `BLEU`, `Q^2` a critic-based faithfulness metriky.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Autori kombinujú `formálnu analytickú deriváciu` s `kvantitatívnym benchmarkovým porovnaním`.

Na analytickej úrovni skúmajú:

- normu gradient mismatch,
- chybu merged modelu voči target modelu,
- implicitné predpoklady rôznych merge schém,
- a bayesovskú interpretáciu merging-u cez Laplace aproximáciu.

Na experimentálnej úrovni sledujú:

- accuracy pri task addition,
- priemerný výkon naprieč sentiment datasetmi,
- robustnosť voči scaling hyperparametrom,
- faithfulness / hallucination metrics,
- toxic generation rate,
- perplexity a fluency.

Silnou stránkou analýzy je, že autori explicitne ukazujú koreláciu medzi gradient mismatch a test error, teda spájajú teoretický pojem priamo s praktickým výkonom merge modelu.

## Hlavné zistenia

Článok ukazuje, že merge výkon úzko súvisí s gradient mismatch a že jeho znižovanie vedie k lepším merged modelom.

Hlavné zistenia sú:

- medzi gradient mismatch a test error existuje jasná korelácia,
- uncertainty-based gradient matching zlepšuje výsledky oproti bežnému averagingu, `task arithmetic` aj `Fisher averaging`,
- pri task addition na `RoBERTa` sa ich metóda dostáva bližšie k `all-data` target modelu než väčšina porovnávaných merge funkcií,
- ich prístup je výrazne robustnejší voči voľbe scaling faktorov; v situáciách, kde `TA` padá pod baseline, ich metóda ostáva stabilná,
- prínos sa ukazuje na `ViT`, encoderových LM aj generatívnych LLM,
- pri odstraňovaní toxicity a halucinácií znižuje nežiadúce správanie viac než TA, často pri menšom poklese fluency alebo perplexity,
- diagonálny Fisher ako lacná aproximácia funguje prakticky dostatočne dobre a robí metódu škálovateľnou.

Pre DP je dôležité, že článok neposúva merge len empiricky, ale ukazuje mechanizmus, podľa ktorého možno navrhovať lepšie merge weighting schémy.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Článok je pre DP veľmi užitočný ako metodický a analytický most medzi jednoduchým task arithmetic a sofistikovanejšími merge prístupmi.

Jeho hodnota pre tému `Kombinácia znalostí veľkých jazykových modelov` spočíva v tom, že:

- rieši priamo `model merging` cez váhové kombinácie,
- vysvetľuje, prečo merge môže zlyhávať pri konfliktných alebo zle škálovaných update-och,
- prináša nový merge mechanizmus založený na `uncertainty` a `gradient matching`,
- prepája task arithmetic, Fisher weighting a bayesovský pohľad do jedného rámca,
- pokrýva nielen multi-task addition, ale aj selective removal nežiaducich schopností.

Pre DP je to dôležitý článok najmä pri porovnaní jednoduchých merge heuristík s metodicky odôvodnenými váhovacími schémami.

## Dôkazové citácie / oporné časti článku

- Motivácia:
  - "The reasons behind the effectiveness of weighted-averaging methods are not well understood."
  - "Ideally, we would like to understand the effect of averaging schemes on the accuracy of the merged model and use it to design better merging methods."

- Hlavná myšlienka:
  - "we first connect the inaccuracy of weighted-averaging to mismatches in the gradients"
  - "improve its performance by reducing the mismatch with a second-order approximation"

- Vzťah k uncertainty:
  - "Both the gradient-mismatch connection and the new method are closely related to uncertainty estimation via approximate Bayesian methods."
  - "the proposed method is much more robust to the choice of scaling factors as scaling naturally appears in its derivation"

- Hlavné výsledky:
  - "reducing mismatch reduces test error"
  - "our method gets closer to the 'all-data' target model"
  - "our method reduces toxicity in comparison to TA"

- Význam pre existujúce metódy:
  - "The connection also reveals implicit assumptions in other schemes such as averaging, task arithmetic, and Fisher-weighted averaging."

