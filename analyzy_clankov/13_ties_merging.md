# TIES-Merging: Resolving Interference When Merging Models

## Fáza A: bibliografická karta

- Názov: TIES-Merging: Resolving Interference When Merging Models
- Autori: Prateek Yadav, Derek Tam, Leshem Choshen, Colin Raffel, Mohit Bansal
- Rok: 2023
- Typ článku: NeurIPS 2023 + arXiv
- Link: <https://arxiv.org/abs/2306.01708>
- Alternatívny link: <https://openreview.net/forum?id=cP7mbBUn1E>
- Téma: Catastrophic forgetting vs transfer
- Relevancia pre DP: `5/5`

## 1) Motivácia autorov výskumu

Autori riešia problém, že pri raste počtu task-specific fine-tuned modelov je prakticky lákavé zlúčiť ich do jedného multitask checkpointu bez ďalšieho tréningu, no existujúce merge metódy pri väčšom počte modelov často výrazne strácajú výkon. Jednoduché priemerovanie alebo task arithmetic síce fungujú v jednoduchších prípadoch, ale pri merge viacerých úloh narážajú na interferenciu medzi parametrami.

Motiváciou článku je vysvetliť, odkiaľ táto interferencia vzniká, a navrhnúť merge postup, ktorý ju explicitne redukuje. Autori ukazujú, že problém nie je len v samotnom priemerovaní, ale aj v tom, že do merged modelu vstupujú redundantné zmeny a parametre s protichodným znamienkom.

## 2) Lokalizácia analýzy literatúry

Analýza súvisiacej literatúry je sústredená hlavne v sekciách `1 Introduction` a `2 Related Work`.

Autori prepájajú tri hlavné línie:

- interpoláciu a weight-space konektivitu medzi modelmi,
- model merging fine-tuned checkpointov z rovnakej inicializácie,
- a task vector prístupy.

Ako kľúčové východiská rozoberajú:

- jednoduché `weight averaging`,
- `Task Arithmetic`,
- `Fisher Merging`,
- `RegMean`,
- a práce o mode connectivity, permutation alignment a optimal transport.

Ich príspevok voči literatúre spočíva v tom, že predchádzajúce metódy sa sústredia na váženie alebo zarovnanie modelov, ale neriešia explicitne `parameter interference`, najmä `redundant parameter values` a `sign disagreement`.

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je navrhnúť merge metódu, ktorá zníži interferenciu medzi task-specific update-mi a tým zlepší výsledok spájania viacerých modelov bez ďalšieho tréningu.

Hlavnú výskumnú otázku možno formulovať takto:

**Aké typy interferencie vznikajú pri merge task vectorov a dá sa ich odstrániť tak, aby merged model lepšie zachoval znalosti z viacerých úloh?**

Vedľajšie otázky sú:

- či sú malé zmeny parametrov po fine-tuningu často redundantné,
- ako veľmi merge poškodzuje `sign conflict`,
- ako sa výkon mení pri raste počtu merge-nutých modelov,
- a či sa dá získať robustný merge aj bez validačnej množiny.

## 4) Koncepčný rámec článku

Koncepčný rámec článku stojí na reprezentácii každého fine-tuned modelu cez `task vector`, teda rozdiel medzi fine-tuned checkpointom a pôvodnou inicializáciou.

Autori identifikujú dva hlavné zdroje interferencie:

- `redundant parameters`: mnohé malé zmeny po fine-tuningu sú pre danú úlohu málo dôležité, no pri merge znižujú magnitúdu skutočne dôležitých update-ov,
- `sign disagreement`: rôzne tasky posúvajú ten istý parameter opačným smerom, a jednoduchý priemer preto oslabí oba signály.

Na tomto základe navrhujú `TIES-Merging`, kde sa merge skladá z troch krokov:

- `Trim`: ponechať len top-k percent najväčších zmien,
- `Elect`: zvoliť finálne znamienko podľa najväčšej agregovanej magnitúdy,
- `Disjoint Merge`: priemerovať len tie hodnoty, ktoré sú v súlade so zvoleným znamienkom.

Výsledkom je rámec, ktorý merge nechá prejsť len cez influentné a navzájom sign-consistent parametre.

## 5) Použitá metóda (stratégia) výskumu

Ide o `metodicko-empirický` článok s návrhom nového training-free merge algoritmu.

Praktický postup je:

1. z každého modelu sa vytvorí `task vector`,
2. každý task vector sa `trimne` na top-k percent parametrov podľa magnitúdy,
3. pre každý parameter sa `electne` finálne znamienko podľa väčšieho celkového pohybu,
4. spraví sa `disjoint mean` len cez tie task vector hodnoty, ktorých znamienko súhlasí so zvoleným znamienkom,
5. výsledný merged task vector sa škáluje cez `lambda` a pripočíta k inicializácii.

Metóda je porovnávaná s:

- `Simple Averaging`,
- `Task Arithmetic`,
- `Fisher Merging`,
- `RegMean`.

Autori navyše analyzujú aj variant bez validačnej množiny, kde navrhujú všeobecný recept `top-20% + lambda = 1`.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Autori testujú metódu vo viacerých nastaveniach:

- `PEFT` merge,
- plný fine-tuning v NLP,
- plný fine-tuning vo vision,
- out-of-domain generalizácia,
- merge checkpointov z tej istej úlohy,
- merge ako lepšia inicializácia pre následný fine-tuning.

Pre PEFT používajú `T0-3B` s `IA3` a 11 NLP datasetov:

- `COPA`,
- `H-SWAG`,
- `Story Cloze`,
- `ANLI`,
- `CB`,
- `RTE`,
- `WSC`,
- `Winogrande`,
- `WiC`.

Pre fully fine-tuned NLP používajú `T5-base` a `T5-large` na siedmich úlohách vrátane:

- `QASC`,
- `WikiQA`,
- `QuaRTz`,
- `PAWS`,
- `Story Cloze`,
- `Winogrande`,
- `WSC`.

Pre vision používajú `CLIP` s `ViT-B/32` a `ViT-L/14` na datasetoch:

- `Cars`,
- `DTD`,
- `EuroSAT`,
- `GTSRB`,
- `MNIST`,
- `RESISC45`,
- `SUN397`,
- `SVHN`.

Out-of-domain generalizáciu merajú na ďalších datasetoch z `T0 mixture`, napríklad:

- `Cosmos QA`,
- `Social IQA`,
- `QuAIL`,
- `WiC`,
- `COPA`,
- `H-SWAG`.

Doplnkovo robia experimenty aj na GLUE checkpointoch a na model soups-like nastavení.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Autori používajú `benchmarkové porovnanie`, `škálovacie experimenty` a `ablation studies`.

Sledujú najmä:

- priemerný výkon na in-domain úlohách,
- výkon na held-out out-of-domain úlohách,
- zmenu výkonu pri raste počtu merge-nutých modelov,
- správanie pri dostupnej a nedostupnej validačnej množine,
- a v doplnkových experimentoch aj robustnosť na checkpoint merging a kvalitu inicializácie.

Dôležitou súčasťou analýzy je aj interpretácia parametrov:

- ako trimming ovplyvňuje magnitúdu merged parametrov,
- ako sign agreement súvisí s deštruktívnou interferenciou,
- aký efekt má chybné otočenie znamienok veľkých parametrov,
- a ktoré kroky `TIES` sú najdôležitejšie podľa ablácií.

## Hlavné zistenia

Článok ukazuje, že výkon merge výrazne poškodzuje interferencia medzi task vector parametrami a že jej explicitné riešenie vedie k lepším multitask modelom.

Hlavné zistenia sú:

- veľká časť parametrov v task vectoroch je redundantná a možno ju odstrániť bez citeľnej straty výkonu,
- `sign conflicts` pretrvávajú aj po trimovaní a ich frekvencia rastie s počtom merge-nutých modelov,
- `TIES-Merging` konzistentne prekonáva `Task Arithmetic`, `RegMean`, `Fisher Merging` aj jednoduché priemerovanie,
- pri in-domain hodnotení zlepšuje najsilnejší baseline v priemere približne o `2.3%` v NLP a `1.7%` vo vision,
- pri out-of-domain generalizácii prekonáva najsilnejší baseline o `1.0%` na `T5-base` a `4.4%` na `T5-large`,
- pri raste počtu úloh degraduje pomalšie než `Task Arithmetic`, čo potvrdzuje lepšiu odolnosť voči rastúcej interferencii,
- aj bez validačnej množiny ostáva veľmi silný; v niektorých nastaveniach dokonca prekoná baseline, ktoré validačnú množinu používajú,
- experiment s `oracle sign` ukazuje, že správne odhadnuté znamienka sú kľúčové a že merge sa vie priblížiť multitask modelu oveľa viac, než naznačujú bežné heuristiky,
- ablačné štúdie potvrdzujú, že všetky časti metódy sú dôležité, najmä `disjoint mean`, `scaling` a `elect`.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Toto je jeden z najdôležitejších článkov pre DP, pretože veľmi priamo rieši kombinovanie znalostí vo weight space a zároveň explicitne tematizuje konflikt medzi transferom a interferenciou.

Je silne relevantný preto, že:

- pracuje priamo s `task vectors` a merge checkpointov,
- analyzuje, prečo sa knowledge combination kazí pri väčšom počte úloh,
- zavádza praktický training-free algoritmus s jasnou interpretáciou,
- prepája performance merge-u s témou `catastrophic forgetting vs transfer`,
- a patrí medzi kľúčové referencie pre moderné LLM / foundation model merging.

Pre diplomovú prácu je mimoriadne cenný ako článok, ktorý nielen navrhuje metódu, ale aj vysvetľuje mechanizmy konfliktu medzi task-specific update-mi.

## Dôkazové citácie / oporné časti článku

- Motivácia:
  - "existing merging methods often ignore the interference between parameters of different models, resulting in large performance drops when merging multiple models."
  - "prior merging techniques inadvertently lose valuable information due to two major sources of interference"

- Dva typy interferencie:
  - "interference due to redundant parameter values"
  - "disagreement on the sign of a given parameter's values across models"

- Jadro metódy:
  - "TRIM, ELECT SIGN & MERGE (TIES-MERGING)"
  - "resetting parameters that only changed a small amount during fine-tuning"
  - "resolving sign conflicts"
  - "merging only the parameters that are in alignment with the final agreed-upon sign"

- Hlavné výsledky:
  - "TIES-MERGING outperforms several existing methods in diverse settings"
  - "the impact of different types of interference on model parameters, and highlight the importance of resolving sign interference"

- Význam správnych znamienok:
  - "TIES-MERGING can perform close to multitask models if the signs can be estimated correctly."
  - "Flipping the signs of high magnitude parameters leads to catastrophic performance drops."

