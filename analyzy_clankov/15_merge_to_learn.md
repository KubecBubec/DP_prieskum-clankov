# Merge to Learn: Efficiently Adding Skills to Language Models with Model Merging

## Fáza A: bibliografická karta

- Názov: Merge to Learn: Efficiently Adding Skills to Language Models with Model Merging
- Autori: Jacob Morrison, Noah A. Smith, Hannaneh Hajishirzi, Pang Wei Koh, Jesse Dodge, Pradeep Dasigi
- Rok: 2024
- Typ článku: Findings of EMNLP 2024 + arXiv
- Link: <https://arxiv.org/abs/2410.12937>
- Alternatívny link: <https://aclanthology.org/2024.findings-emnlp.915/>
- Téma: Catastrophic forgetting vs transfer
- Relevancia pre DP: `5/5`

## 1) Motivácia autorov výskumu

Autori riešia praktický problém, ako do už instruction-tuned jazykového modelu efektívne dopĺňať nové skill-specific schopnosti. Bežné riešenia sú buď `continued finetuning`, ktoré je lacné, ale vedie k zabúdaniu starších schopností, alebo `retraining`, ktoré je výkonovo silné, ale výpočtovo drahé a často nemožné bez prístupu k pôvodným tréningovým dátam.

Motiváciou článku je overiť, či sa nové skills dajú pridávať lacnejšie a bezpečnejšie cez paralelný tréning na nových dátach a následný merge. Autori chcú ukázať, že model merging môže slúžiť ako praktický mechanizmus `model patching`, ktorý rozšíri model bez veľkej straty generalistických schopností.

## 2) Lokalizácia analýzy literatúry

Analýza literatúry je sústredená hlavne v úvode, v sekcii `2 Problem Setup` a v `5 Related Work`.

Autori prepájajú tri línie:

- `continued finetuning` ako klasický spôsob dopĺňania nových skillov,
- `retraining from scratch` s aktualizovaným data mixom,
- a `parallel train then merge (PTM)` ako rodinu merge-based patching prístupov.

Ako merge referencie uvádzajú:

- `task arithmetic`,
- `linear interpolation`,
- `WiSE-FT`,
- a širšie súvislosti s model patching a skill injection.

Ich pozícia je, že doteraz chýbalo systematické porovnanie týchto troch ciest špecificky pre `instruction tuning`, teda pre situáciu, kde chceme dopĺňať nové schopnosti bez zničenia general skills.

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je systematicky porovnať tri spôsoby pridávania nových skillov do instruction-tuned modelov:

- `continued finetuning (CFT)`,
- `retraining (RT)`,
- `parallel train then merge (PTM)`.

Hlavnú výskumnú otázku možno sformulovať takto:

**Dokáže parallel-train-then-merge pridávať nové skills do instruction-tuned modelov tak, aby zachoval všeobecné schopnosti modelu a zároveň bol výrazne lacnejší než retraining?**

Vedľajšie otázky sú:

- či PTM prekoná CFT v zachovaní general skills,
- či sa priblíži retrainingu v specialized performance,
- ako zvoliť merge váhu `omega`, ak nie je k dispozícii validačný set,
- a ako sa PTM správa pri pridávaní viacerých skillov naraz.

## 4) Koncepčný rámec článku

Koncepčný rámec článku chápe pridávanie nových skillov ako problém `model patching`. Máme:

- všeobecný instruction-tuned model `theta_G`,
- nový skill-specific dataset `D`,
- a cieľ zlepšiť výkon na `E_D` bez poškodenia výkonu na `E_G`.

Autori definujú `PTM` ako dvojkrokový rámec:

1. natrénovať skill-specific model iba na novom datasete,
2. následne ho skombinovať s generalist modelom vo weight space.

V tomto rámci študujú tri merge varianty:

- `task arithmetic`,
- `linear interpolation`,
- `WiSE-FT`.

Ich hlavná hypotéza je, že PTM je vhodný práve preto, že priamo nemení váhy naučené na starých general tasks počas tréningu na nových dátach, a preto by mal lepšie zachovávať pôvodné schopnosti modelu než continued finetuning.

## 5) Použitá metóda (stratégia) výskumu

Ide o `porovnávací empirický` článok so silnou metodickou zložkou.

Autori explicitne porovnávajú:

- `CFT`: pokračovanie fine-tuningu generalist modelu na nových dátach,
- `RT`: retraining od pretrained checkpointu na zmesi pôvodných a nových dát,
- `PTM`: paralelný tréning na novom skille a následný merge.

V PTM skúmajú najmä `task arithmetic`, kde:

- sa na skill-specific dátach natrénuje model `theta_D`,
- odpočíta sa pretrained model a vznikne task vector,
- task vector sa s váhou `omega` pridá ku general modelu.

Popri tom testujú aj:

- `linear interpolation`,
- `WiSE-FT`.

Autori tiež analyzujú výpočtovú cenu jednotlivých prístupov cez počet tréningových krokov a navrhujú praktickú heuristiku pre voľbu merge váhy:

- `omega = |D| / |G|`.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Autori experimentujú na troch typoch nových skillov:

- `Science`,
- `Safety`,
- `Coding`.

Použité datasety:

- general model: modifikovaný `Tulu V2` mix,
- science: `SciRIFF`,
- safety: interný refusal dataset,
- coding: `CodeFeedback`.

General skills hodnotia na podmnožine eval suite pre `Tulu 2`, ktorá zahŕňa:

- `MMLU`,
- `GSM8K`,
- `AlpacaEval`,
- `Big Bench Hard`,
- `TruthfulQA`.

Science hodnotia cez `SciRIFF` validation/test na deviatich held-out taskoch. Coding hodnotia cez:

- `HumanEval+`,
- `MBPP+`.

Safety hodnotia cez:

- `ToxiGen`,
- `HarmBench`,
- `XSTest Unsafe`,
- a osobitne `XSTest Safe` pre `exaggerated refusals`.

Všetky experimenty bežia na `Llama 2 7B`, pričom modely sú plne fine-tunované na dve epochy.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Analýza je založená na porovnaní `general performance`, `specialized performance` a `training cost`.

Autori sledujú:

- výkon na general evaloch,
- výkon na skill-specific evaloch,
- zmenu oproti baseline modelu,
- počet tréningových krokov ako mieru nákladov,
- trade-off krivky pri rôznych hodnotách `omega`,
- a správanie pri pridávaní jedného aj viacerých skillov.

Osobitnú pozornosť venujú:

- `exaggerated refusals`, teda prehnanému odmietaniu neškodných promptov,
- heuristike výberu `omega` bez validačných dát,
- a interferencii pri merge viacerých skill-specific vektorov naraz.

## Hlavné zistenia

Článok ukazuje, že `PTM` je veľmi silný kompromis medzi kvalitou, zachovaním pôvodných schopností a cenou.

Hlavné zistenia sú:

- pri optimalizácii pre nový skill je `PTM` konkurencieschopný voči najlepšiemu `RT`, ale je o `50-95%` lacnejší,
- oproti `CFT` zachováva takmer všetky pôvodné general skills, zatiaľ čo `CFT` spôsobuje pokles približne `10-40%`,
- pri science úlohe dosahuje `PTM` podobný výkon ako najlepší `RT`, ale za približne `4%` jeho tréningovej ceny,
- pri safety skille je `PTM` obzvlášť silný: dosahuje podobnú alebo lepšiu safety výkonnosť ako `RT`, pritom lepšie zachováva general skills,
- pri `exaggerated refusals` výrazne prekonáva `CFT` aj `RT`, pričom zlepšuje compliance na bezpečných, ale potenciálne zavádzajúcich promptoch,
- heuristika `omega = |D| / |G|` funguje prekvapivo dobre aj bez validačného setu,
- pri pridávaní viacerých skillov naraz dokáže PTM zlepšiť general performance, safety aj coding bez dodatočného tréningu, ale science skill trpí interferenciou,
- párové merge experimenty naznačujú, že pokles science výkonu pri three-skill merge je spôsobený najmä interferenciou medzi safety a coding vektormi,
- z alternatívnych PTM metód sú `linear interpolation` a `WiSE-FT` často silné na špecifickom skille, ale horšie zachovávajú general skills než task arithmetic.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Tento článok je pre DP veľmi dôležitý, pretože rieši presne tému pridávania novej znalosti do už existujúceho modelu pomocou weight-space kombinácie.

Je silne relevantný najmä tým, že:

- porovnáva merging s dvoma prirodzenými alternatívami (`CFT`, `RT`),
- používa merge ako praktický nástroj pre `adding new skills`,
- kladie dôraz na zachovanie pôvodných schopností modelu,
- ukazuje aj multi-skill merge a problém interferencie,
- a prináša veľmi použiteľnú heuristiku pre merge bez validačných dát.

Pre diplomovú prácu je to jeden z kľúčových článkov k otázke, či sa nové schopnosti dajú do LLM dopĺňať lacno a bez výrazného forgettingu.

## Dôkazové citácie / oporné časti článku

- Motivácia:
  - "Adapting general-purpose language models to new skills is currently an expensive process ... or can cause the models to forget older skills."
  - "CFT ... may cause the model to forget the skills from earlier rounds of training."

- Hlavná myšlienka:
  - "parallel train then merge"
  - "PTM does not require access to the original training data"
  - "PTM is consistently a better choice than CFT for teaching instruction-tuned models new skills without compromising on general skills."

- Výsledky:
  - "PTM achieves competitive performance with the best RT models, with a 50-95% improvement in training efficiency."
  - "PTM preserves nearly all of the original model's general skills versus a 10-40% drop for CFT"
  - "For enabling safety-related refusals ... PTM proves to be particularly effective"

- Heuristika:
  - "Setting the mixture weight proportional to the ratio of the number of training steps dedicated to the new skill being added is a good heuristic"

- Multi-skill interferencia:
  - "the drop in science performance is most likely caused by interference between the coding and safety vectors"

