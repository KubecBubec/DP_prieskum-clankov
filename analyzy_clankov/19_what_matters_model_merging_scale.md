# What Matters for Model Merging at Scale?

## Fáza A: bibliografická karta

- Názov: What Matters for Model Merging at Scale?
- Autori: Prateek Yadav, Tu Vu, Jonathan Lai, Alexandra Chronopoulou, Manaal Faruqui, Mohit Bansal, Tsendsuren Munkhdalai
- Rok: 2024
- Typ článku: TMLR + arXiv
- Link: <https://arxiv.org/abs/2410.03617>
- Alternatívny link: <https://openreview.net/forum?id=9sbetmvNpW>
- Téma: Evaluation / benchmarking of merged models
- Relevancia pre DP: `5/5`

## 1) Motivácia autorov výskumu

Autori reagujú na to, že výskum model merging-u sa dlho opieral najmä o malé modely, málo expertov a úzke benchmarky. Preto zostávalo nejasné, ako sa merge správa pri väčších jazykových modeloch a akú úlohu zohráva kvalita base modelu, veľkosť modelu a počet merge-nutých expertov.

Motiváciou článku je odpovedať na otázku, čo skutočne rozhoduje o úspechu merge-u vo veľkom meradle. Nejde len o to, či merge funguje, ale za akých podmienok funguje dobre na held-in taskoch a kedy zlepšuje aj `zero-shot generalization` na held-out úlohách.

## 2) Lokalizácia analýzy literatúry

Analýza literatúry je sústredená najmä v `1 Introduction`, `2 Background`, `2.1 Model Merging Methods` a `5 Related Work`.

Autori prepájajú:

- klasické `parameter averaging`,
- `Task Arithmetic`,
- `TIES`,
- `Dare-TIES`,
- a širší kontext loss landscape, mode connectivity a weight interpolation.

Ich pozícia voči literatúre je, že doterajšie práce síce ukázali sľubné výsledky, ale väčšinou:

- mergovali iba `2-3` expertov,
- pracovali s modelmi pod `7B`,
- alebo sa sústredili hlavne na `held-in` benchmarky.

Tento článok posúva diskusiu smerom ku scaling otázkam: aký vplyv má veľkosť modelu, kvalita inicializácie a počet expertov na úspešnosť merge-u.

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je systematicky preskúmať, ktoré faktory sú rozhodujúce pre úspešný merge vo veľkom meradle.

Autori si explicitne kladú štyri otázky:

1. pomáha, ak experti vznikli zo `strong instruction-tuned base modelu` namiesto čistého pretrained modelu?
2. stáva sa merge s rastúcou veľkosťou modelu ľahším?
3. zlepšuje merge `held-out zero-shot generalization`?
4. koľko expert modelov možno úspešne zlúčiť a ako to závisí od veľkosti modelu?

Hlavný cieľ teda nie je navrhnúť novú metódu, ale odvodiť praktické scalingové zákonitosti a odporúčania pre merging veľkých jazykových modelov.

## 4) Koncepčný rámec článku

Koncepčný rámec článku stojí na rozlíšení dvoch evaluačných režimov:

- `held-in`: výkon na taskoch, na ktorých boli experti trénovaní,
- `held-out`: zero-shot generalizácia na nových úlohách.

Autori chápu merge ako spôsob, ako vytvoriť unified model, ktorý:

- zachová capabilities expertov na ich vlastných taskoch,
- a ideálne zlepší generalizáciu nad rámec toho, čo vie samotný base model alebo multitask baseline.

V rámci tohto rámca sledujú tri hlavné faktory:

- `base model quality`,
- `model size`,
- `number of experts`.

Koncepčne je dôležité, že článok nehodnotí len absolútny výkon, ale najmä to, ako sa tieto faktory navzájom ovplyvňujú pri merge.

## 5) Použitá metóda (stratégia) výskumu

Ide o `systematický empirický` článok.

Autori používajú:

- `PaLM-2` a `PaLM-2-IT`,
- modelové veľkosti `1B`, `8B`, `24B`, `64B`,
- a merge až `8` expert modelov.

Porovnávajú štyri prakticky škálovateľné merge metódy:

- `Averaging`,
- `Task Arithmetic`,
- `TIES-Merging`,
- `Dare-TIES`.

Zámerne sa vyhýbajú metódam, ktoré pri veľkých modeloch vyžadujú dodatočné štatistiky, spätné prechody alebo iné nákladné pomocné informácie, pretože ich cieľom je analyzovať realisticky škálovateľný merge.

Vyhodnotenie robia cez sériu citlivostných a ablačných experimentov, v ktorých menia:

- typ base modelu,
- veľkosť modelu,
- počet expertov,
- merging method,
- a následne sledujú held-in aj held-out výkon.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Autori stavajú experimenty na kolekcii `T0` úloh, kde rozlišujú:

- `held-in tasks`, na ktorých boli experti trénovaní,
- `held-out tasks`, ktoré slúžia na zero-shot generalizáciu.

Používajú expert modely vytvorené plným fine-tuningom z dvoch typov base modelov:

- `PaLM-2`,
- `PaLM-2-IT`.

Získavajú tak široké spektrum scenárov:

- malé aj veľmi veľké modely,
- slabšie aj silnejšie base inicializácie,
- merge malého aj väčšieho počtu expertov.

Presné tasky sú organizované tak, aby bolo možné agregovať výkon do jednotných metrik naprieč held-in a held-out evalom.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Analýza je založená na porovnaní normalizovaného výkonu merged modelov voči:

- oracle expert modelu na held-in úlohách,
- base modelu pri held-out generalizácii,
- a multitask baseline.

Autori sledujú najmä:

- priemerný `held-in` výkon,
- `held-out zero-shot generalization`,
- zmenu výkonu so zvyšovaním počtu expertov,
- a interakciu medzi veľkosťou modelu a kvalitou base modelu.

Dôležité je, že porovnávajú nielen merge metódy navzájom, ale aj proti multitask trainingu, čo dáva veľmi praktický obraz o tom, kedy sa merge naozaj oplatí.

## Hlavné zistenia

Článok ukazuje, že úspešnosť merge-u vo veľkom meradle závisí viac od kvality base modelu a veľkosti modelu než od samotnej sofistikovanosti merge algoritmu.

Hlavné zistenia sú:

- merge funguje výrazne lepšie, keď experti vznikli zo `strong instruction-tuned base modelu` než z čistého pretrained modelu,
- väčšie modely sa merge-nú ľahšie; s rastúcou veľkosťou modelu rastie held-in výkon merged modelov,
- merge konzistentne zlepšuje `held-out zero-shot generalization`,
- pri veľkých a silných base modeloch môže merged model na held-out úlohách dorovnať alebo prekonať `multitask` baseline,
- väčšie modely zvládajú merge väčšieho počtu expertov s menšou degradáciou,
- pri slabších base modeloch sa held-in výkon s rastúcim počtom expertov skôr zhoršuje, kým pri silných instruction-tuned modeloch sa stabilizuje na vysokej úrovni,
- na veľkých instruction-tuned modeloch sa rozdiely medzi merge metódami zmenšujú; jednoduché metódy ako `Averaging` alebo `Task Arithmetic` sa približujú zložitejším variantom,
- článok preto naznačuje, že pri scale je dôležitejšia `quality of experts` a `model capacity` než jemné rozdiely medzi merge algoritmami.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Tento článok je pre DP veľmi dôležitý, pretože odpovedá na praktickú otázku, od čoho merge v skutočnosti závisí pri veľkých jazykových modeloch.

Je veľmi relevantný najmä tým, že:

- explicitne skúma `scale`,
- prepája merge s `held-in` aj `held-out` generalizáciou,
- analyzuje počet expertov a kvalitu base modelu,
- porovnáva merge s multitask tréningom,
- a dáva priamo použiteľné odporúčania pre prax.

Pre diplomovú prácu je to silný článok k diskusii o podmienkach úspešného merge-u, najmä pri vysvetľovaní, prečo merge niekedy funguje výborne a inokedy zlyháva.

## Dôkazové citácie / oporné časti článku

- Motivácia:
  - "previous studies have primarily focused on merging a few small models"
  - "it remains unclear how scale interplays with other factors like number of expert models and base model quality"

- Hlavné zistenia:
  - "merging is more effective when experts are created from strong base models"
  - "larger models facilitate easier merging"
  - "merging consistently improves generalization capabilities"
  - "we can better merge more expert models when working with larger models"

- Dôležitý praktický point:
  - "when using strong base models as the number of merged experts increases, our merged model either matches or exceeds the performance of a strong multi-task training baseline"

- O metódach pri scale:
  - "different merging methods behave very similarly at larger scales"
  - "using simple merging methods like averaging will result in models that are comparable in quality with the models obtained from more advanced merging method"

