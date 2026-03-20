# A Systematic Study of Model Merging Techniques in Large Language Models

## Fáza A: bibliografická karta

- Názov: A Systematic Study of Model Merging Techniques in Large Language Models
- Autori: Oguz Kagan Hitit, Leander Girrbach, Zeynep Akata
- Rok: 2025
- Typ článku: arXiv preprint
- Link: <https://arxiv.org/abs/2511.21437>
- Téma: Evaluation / benchmarking of merged models
- Relevancia pre DP: `5/5`

## 1) Motivácia autorov výskumu

Autori reagujú na to, že model merging je často prezentovaný ako veľmi sľubný aj pre veľké jazykové modely, no veľká časť dôkazov pochádza z menších modelov alebo z anekdotických príkladov. Nie je preto jasné, či sa pozorované výhody, vrátane `constructive interference`, skutočne prenášajú na moderné open-weight LLM.

Motiváciou článku je urobiť systematický a rozsiahly test, ktorý oddelí reálne robustné trendy od optimizmu založeného na malých alebo selektívnych experimentoch. Autori chcú zistiť, ktoré merge metódy na LLM skutočne fungujú, za akých podmienok a či novšie interference-aware alebo subspace prístupy prinášajú reálny prínos oproti jednoduchej task arithmetic.

## 2) Lokalizácia analýzy literatúry

Analýza literatúry je koncentrovaná najmä v sekciách `2 Related Work`, `2.1 Background`, `2.2 Detailed Overview of Model Merging Techniques and Paradigms` a `2.3 Practical Applications of Model Merging`.

Autori rozlišujú:

- klasické `weight interpolation` metódy,
- `interference-aware` modifikácie task arithmetic,
- `subspace merging` prístupy,
- a praktické aplikácie merging-u v continual learning, robustness či alignment.

Ako kľúčové metodické línie rozoberajú:

- `Task Arithmetic`,
- `TIES`,
- `DARE`,
- `Model Stock`,
- `TSV-Merge`,
- `Iso-C`,
- `Subspace Boosting`.

Ich literárny prínos je v tom, že spájajú staršie weight-space prístupy s novšími subspace metódami do jedného jednotného evaluačného rámca pre LLM, čo podľa nich doteraz chýbalo.

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je systematicky overiť, či existujúce merge metódy naozaj prinášajú `constructive interference` pri moderných LLM a či sa výsledky zo sveta menších modelov prenášajú na large language models.

Hlavnú výskumnú otázku možno sformulovať takto:

**Ktoré model merging techniky spoľahlivo prinášajú zisk na moderných open-weight LLM a dokážu merged model dostať nad base model alebo dokonca nad najlepší individuálny checkpoint?**

Vedľajšie otázky sú:

- či interference-aware metódy pomáhajú aj pri heterogénnych LLM checkpointoch,
- či subspace metódy fungujú na LLM rovnako dobre ako na menších alebo vision-language modeloch,
- ako sa výkon mení s počtom merge-nutých expertov,
- a akú úlohu hrá vzdialenosť merged modelu od base checkpointu.

## 4) Koncepčný rámec článku

Koncepčný rámec článku stojí na pojme `constructive interference`, teda situácii, keď merged model prekoná nielen base model, ale aj individuálne expert checkpointy. To je hlavný test, ktorým autori posudzujú skutočný úspech merge-u.

V rámci tohto rámca porovnávajú dve veľké rodiny metód:

- `task-arithmetic / weight interpolation` prístupy,
- `subspace-based` prístupy.

Predpoklad, ktorý článok priebežne testuje, je, že moderné LLM checkpointy po rôznych fine-tuningoch nemusia spĺňať geometrické predpoklady, na ktorých stoja komplikovanejšie merge algoritmy. Ak task updates netvoria stabilné alebo kompatibilné subpriestory, agresívnejšie zásahy môžu merged model odtlačiť príliš ďaleko od base modelu a spôsobiť forgetting namiesto synergie.

## 5) Použitá metóda (stratégia) výskumu

Ide o `systematický benchmarkovo-empirický` článok.

Autori pre každý base model zbierajú `12` verejne dostupných fine-tuned checkpointov, z ktorých opakovane vzorkujú podmnožiny rôznych veľkostí a tie následne merge-nú vybranými metódami. Výsledné merged modely potom vyhodnocujú na štandardných LLM benchmarkoch.

V prvej časti porovnávajú tri task-arithmetic–based metódy:

- `Task Arithmetic`,
- `TIES-Merging`,
- `Model Stock`.

V druhej časti porovnávajú tri subspace metódy:

- `TSV-Merge`,
- `Iso-C`,
- `Subspace Boosting`.

Dôležité je, že všetky metódy testujú pod rovnakým protokolom:

- tie isté base modely,
- tie isté checkpointy,
- tie isté sampled subsets,
- ten istý eval stack cez `mergekit` a `lm-eval-harness`.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Autori testujú štyri open-weight base modely:

- `Llama-3.2-3B`,
- `Llama-3.1-8B`,
- `Qwen3-4B`,
- `Qwen3-8B`.

Pre každý z nich používajú `12` fine-tuned checkpointov. Vyhodnotenie prebieha na `16` štandardných benchmarkoch z `lm-evaluation-harness`, medzi nimi napríklad:

- `arc_easy`,
- `arc_challenge`,
- `hellaswag`,
- `winogrande`,
- `boolq`,
- `piqa`,
- `openbookqa`,
- `commonsense_qa`,
- `headqa`,
- `prost`,
- `truthfulqa_mc1`,
- `mmlu`,
- `medmcqa`,
- `leaderboard_gpqa`,
- `leaderboard_bbh`,
- `leaderboard_mmlu_pro`.

Tieto benchmarky pokrývajú commonsense reasoning, vedecké otázky, viac-krokové uvažovanie aj širšie language understanding schopnosti.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Analýza je založená na opakovanom merge-nutí rôzne veľkých podmnožín expert checkpointov a sledovaní:

- priemernej accuracy naprieč benchmarkmi,
- pravdepodobnosti, že merged model prekoná `base model`,
- relatívneho zisku voči `best individual checkpoint`,
- a L2 normy vzdialenosti merged modelu od base checkpointu.

To je dôležité, pretože autori nechcú ukázať len priemerné skóre, ale aj:

- ako často merge skutočne funguje,
- ako sa správanie mení s počtom expertov `n`,
- a či výkon súvisí s tým, ako ďaleko sa merged model posunie v parameter space.

Pri subspace metódach navyše robia samostatné ablačné nastavenia a používajú rovnaký eval protokol, aby izolovali efekt samotného merge algoritmu.

## Hlavné zistenia

Článok prináša pomerne skeptický záver: väčšina merge metód, ktoré vyzerajú sľubne na menších modeloch, na moderných LLM nefunguje spoľahlivo.

Hlavné zistenia sú:

- zo všetkých testovaných metód iba `Task Arithmetic` konzistentne prináša `constructive interference` na LLM,
- `Task Arithmetic` s rastúcim počtom expertov zlepšuje úspešnosť aj relatívny zisk; pri väčších `n` často prekonáva nielen base model, ale aj najlepší individuálny checkpoint,
- `Model Stock` je konzervatívny a väčšinou ostáva blízko base modelu, takže zisky sú malé, ale nie katastrofické,
- `TIES-Merging` sa na LLM správa zle a s rastúcim počtom merge-nutých expertov výkon výrazne degraduje,
- autori spájajú zlyhanie `TIES` s tým, že metóda odtlačí model príliš ďaleko od base checkpointu a tým spôsobí `catastrophic forgetting`,
- subspace metódy `TSV-Merge` a `Iso-C` tiež väčšinou zlyhávajú a ich výkon sa s rastúcim počtom expertov zhoršuje,
- `Task Arithmetic + Subspace Boosting` je jediná subspace-related konfigurácia, ktorá sa nezrúti, ale jej úspech podľa autorov pochádza hlavne zo samotnej `Task Arithmetic`,
- kľúčovým indikátorom zlyhania je veľká vzdialenosť `||theta_merged - theta_base||_2`; metódy s veľkým posunom v parameter space zvyknú silnejšie degradovať,
- výsledky naznačujú, že súčasné merge techniky sa na moderné LLM neprenášajú priamo a treba navrhovať `LLM-specific merging algorithms`,
- autori tiež navrhujú smer `merging-aware fine-tuning`, teda fine-tuning, ktorý od začiatku podporuje budúcu kompatibilitu checkpointov pri merge.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Tento článok je pre DP veľmi dôležitý, pretože pôsobí ako protiváha voči optimistickým merge paperom. Ukazuje, že na moderných LLM nemožno automaticky predpokladať, že čím sofistikovanejšia merge metóda, tým lepší výsledok.

Je mimoriadne relevantný preto, že:

- systematicky testuje merge priamo na moderných open-weight LLM,
- hodnotí `constructive interference` ako centrálny cieľ,
- porovnáva klasické aj subspace prístupy,
- explicitne ukazuje limity `TIES` a viacerých novších metód na LLM,
- a poskytuje veľmi silnú motiváciu pre výskumnú otázku o tom, kedy a prečo weight-level combination funguje.

Pre diplomovku je to jeden z kľúčových článkov k časti o limitoch a otvorených problémoch model merging-u.

## Dôkazové citácie / oporné časti článku

- Motivácia:
  - "it remains unclear whether the advantages reported for smaller models and classifiers generalize to LLMs"
  - "what conditions constructive interference reliably emerges, especially for large language models"

- Hlavný výsledok:
  - "Task Arithmetic is the only approach that reliably yields performance gains on LLMs"
  - "Other interference-aware and subspace merging methods typically result in significant performance drops"

- O `Task Arithmetic`:
  - "Task Arithmetic steadily improves as more experts are combined"
  - "merging several independent fine-tuned checkpoints can produce a model that surpasses both the base LLM and any individual expert"

- O zlyhaní `TIES` a iných metód:
  - "TIES-Merging ... suffers from catastrophic performance degradation"
  - "TIES encourages stronger deviation from the base model"

- O subspace metódach:
  - "subspace projection and flattening generally fail to produce constructive interference in LLMs"
  - "subspace-based methods suppress rather than exploit beneficial diversity"

- Záver pre budúci výskum:
  - "This motivates research on LLM-specific merging algorithms and merging-aware fine-tuning methods."

