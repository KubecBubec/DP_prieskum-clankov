# Plán experimentov po konzultácii

Dôležité doplnenie po konzultácii:
- po merge bude nasledovať `dotrénovanie` (post-merge adaptation), nie iba jednorazový merge.

## základné formulácie pre DP a prezentáciu

### Pracovný názov DP

`Znižovanie catastrophic forgetting pri spájaní doménovo špecializovaných Llama modelov bez prístupu k pôvodným tréningovým dátam.`

### Hlavná výskumná otázka

`Ako ovplyvňuje voľba model merging metódy a následného dotrénovania trade-off medzi zachovaním doménových schopností a catastrophic forgetting pri spájaní doménovo špecializovaných Llama modelov bez prístupu k pôvodným tréningovým dátam?`

### Koncepčný rámec

Práca bude postavená na rámci:
- `transfer vs. catastrophic forgetting`,
- `knowledge combination vo weight space`.

Základná myšlienka:
- model merging má preniesť alebo skombinovať doménové schopnosti viacerých expert checkpointov,
- kľúčové riziko je catastrophic forgetting, teda strata pôvodných/general alebo doménových schopností po spojení a dotrénovaní.

### Metóda generovania dát

Dáta pre vyhodnotenie nebudú vznikať dotazníkmi ani rozhovormi, ale experimentálne:
- spustením vybraných modelov na doménových a retention benchmarkoch,
- v stavoch `base`, `expert`, `merged` a `merged + dotrénovanie`.

### Metóda analýzy dát

Použitá bude `kvantitatívna komparatívna analýza`.

Porovnávať sa budú najmä:
- doménový výkon pred a po merge,
- pokles alebo zachovanie výkonu na retention/general benchmarkoch,
- rozdiel medzi variantmi `merged bez FT` a `merged + FT`,
- rozdiely medzi merge metódami a medzi príbuznými vs. vzdialenejšími doménami.

## Scope (MVP pre DP)

- `2 veľkosti` v jednej rodine (jednoduchšia kompatibilita), napríklad:
  - menšia: `Llama-3.2-3B`,
  - väčšia: `Llama-3.1-8B`.
- `3 doménové dvojice` na kombináciu:
  - hlavná dvojica: `Medical/Clinical + Biology/Science`,
  - druhá dvojica: `Legal + Finance`,
  - stress-test dvojica: `Medical/Clinical + Finance`.
- `4 merge metódy`:
  - `Averaging`,
  - `Task Arithmetic`,
  - `TIES`,
  - `DARE`.
- `2 tréningové režimy` po merge:
  - bez dotrénovania (čistý merge baseline),
  - s krátkym dotrénovaním (napr. SFT/continued tuning na dostupných dátach pre cieľovú doménu).

## Konkrétne domény a checkpointy pre experimenty

Aktuálny pracovný výber modelovej rodiny:
- `Llama`, pretože má dobrý ekosystém checkpointov, tooling a existujúcu literatúru k model merging-u.

Pracovné pravidlo:
- nemergovať modely medzi veľkosťami,
- robiť rovnaký typ experimentu samostatne pre `3B` a `8B`,
- pred plným experimentom overiť, či ide o kompatibilné full-weight checkpointy (`safetensors`), nie iba `GGUF`, kvantizáciu alebo samostatný `LoRA` adaptér.

### Doména: Medical / Clinical

#### 3B kandidát

- `Joycean0301/Llama-3.2-3B-Instruct-Medical-Conversational`
  - dôvod výberu: medical conversational/instruct doména, full `safetensors`, vhodné pre menšiu veľkosť.

#### 8B kandidát

- `cc0de/meta-llama-Llama-3.1-8B-full-ft-clinical-bsz16-lr1e-06`
  - dôvod výberu: clinical doména, full `safetensors`, vhodnejšie ako LoRA-only medical modely.

#### 8B záloha

- `CodCodingCode/llama-3.1-8b-clinical`

### Doména: Biology / Science

#### 3B kandidát

- `theprint/LLM-Data-Science-Llama3.2-3B`
  - dôvod výberu: science/data-science doména, full `safetensors`.

#### 8B kandidát

- `mlfoundations-dev/llama3-1_8b_oh_v3.1_wo_camel_ai_biology`
  - dôvod výberu: biology doména, full `safetensors`, tagy ukazujú fine-tune z `meta-llama/Llama-3.1-8B`.

#### 8B záloha

- `lihaoxin2020/Llama-3.1-8B-tulu_all-science_1k`

### Doména: Legal

#### 3B kandidát

- `ahmadsakor/Llama3.2-3B-Instruct-Legal-Summarization`
  - dôvod výberu: legal summarization doména, full `safetensors`.

#### 8B kandidát

- `khalidrajan/Llama-3.1-8B-Instruct-Legal-NLI-Finetuned`
  - dôvod výberu: legal NLI doména, full `safetensors`.

#### 8B záloha

- `airesearch/LLaMa3.1-8B-Legal-ThaiCCL-Combine`
  - poznámka: skôr záloha, pretože je jazykovo špecificky orientovaný na thai legal doménu.

### Doména: Finance

#### 3B kandidát

- `Houbid/llama-3.2-3b-chat-finance-consultant`
  - dôvod výberu: finance consultant/chat doména, full `safetensors`.

#### 8B kandidát

- `Wengwengwhale/llama-3.1-8B-Instruct-finance-analyst`
  - dôvod výberu: finance analyst doména, full `safetensors`, tagy ukazujú fine-tune z `unsloth/Meta-Llama-3.1-8B-Instruct`.

#### 3B záloha

- `TEL-LLM/Llama-3.2-3B-TEL-finance`

## Navrhnuté dvojice pre merge experimenty

### 1. Hlavná dvojica: Medical/Clinical + Biology/Science

#### 3B experiment

- medical: `Joycean0301/Llama-3.2-3B-Instruct-Medical-Conversational`
- science: `theprint/LLM-Data-Science-Llama3.2-3B`

#### 8B experiment

- clinical: `cc0de/meta-llama-Llama-3.1-8B-full-ft-clinical-bsz16-lr1e-06`
- biology: `mlfoundations-dev/llama3-1_8b_oh_v3.1_wo_camel_ai_biology`

Očakávanie:
- príbuzné domény by mohli mať menšiu interferenciu,
- vhodné ako hlavná linka DP pre spojenie odborného poznania.

### 2. Druhá dvojica: Legal + Finance

#### 3B experiment

- legal: `ahmadsakor/Llama3.2-3B-Instruct-Legal-Summarization`
- finance: `Houbid/llama-3.2-3b-chat-finance-consultant`

#### 8B experiment

- legal: `khalidrajan/Llama-3.1-8B-Instruct-Legal-NLI-Finetuned`
- finance: `Wengwengwhale/llama-3.1-8B-Instruct-finance-analyst`

Očakávanie:
- domény sú odlišné, ale obe sú textovo/argumentačne orientované,
- vhodné ako druhá porovnávacia dvojica.

### 3. Stress-test dvojica: Medical/Clinical + Finance

#### 3B experiment

- medical: `Joycean0301/Llama-3.2-3B-Instruct-Medical-Conversational`
- finance: `Houbid/llama-3.2-3b-chat-finance-consultant`

#### 8B experiment

- clinical: `cc0de/meta-llama-Llama-3.1-8B-full-ft-clinical-bsz16-lr1e-06`
- finance: `Wengwengwhale/llama-3.1-8B-Instruct-finance-analyst`

Očakávanie:
- vzdialenejšie domény by mali ukázať väčšiu interferenciu,
- vhodné ako stress-test pre catastrophic forgetting.

## Merge metódy - čo určite a čo voliteľne

### Minimum (určite zahrnúť)

- `Averaging` (baseline),
- `Task Arithmetic` (silný jednoduchý referenčný prístup),
- `TIES`,
- `DARE`.

### Optional (ak ostane čas)

- `SLERP` alebo `RegMean` ako 5. metoda.

## Post-merge dotrénovanie - čo presne porovnať

kontrolovaný experiment:

1. `Base model` (referencia),
2. `Merged model bez dotrénovania`,
3. `Merged model s dotrénovaním`.

- držať rovnaké training budgety medzi variantmi (epochy/kroky),
- logovať čas a nároky (získame aj praktický argument, nielen score).

## Benchmarky a metriky

### Doménové benchmarky (pracovný výber)

- medical/clinical:
  - `MedQA`,
  - `MedMCQA`,
  - `PubMedQA`,
  - prípadne `MMLU` medical/clinical podmnožiny.
- biology/science:
  - `SciQ`,
  - `ARC-Challenge`,
  - `MMLU` biology/chemistry/science podmnožiny.
- legal:
  - `LegalBench`,
  - `LexGLUE`,
  - prípadne doménové legal NLI/summarization datasety podľa kompatibility.
- finance:
  - `Financial PhraseBank`,
  - `FiQA`,
  - `FinQA`,
  - `ConvFinQA`.

### Retention/general benchmarky

- `MMLU` alebo vybrané MMLU podmnožiny mimo cieľovej domény,
- `ARC-Challenge`,
- voliteľne `TruthfulQA` alebo iný general-purpose eval.

### Metriky, ktoré musia byť v prezentácii aj v DP

- delta proti base modelu na cieľovom doménovom benchmarku,
- delta proti najlepšiemu doménovému expert modelu,
- forgetting index (pokles na retention/general benchmarkoch),
- efekt dotrénovania: rozdiel medzi `merged bez FT` vs `merged + FT`.

