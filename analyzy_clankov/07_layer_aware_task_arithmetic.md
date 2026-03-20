# Layer-Aware Task Arithmetic: Disentangling Task-Specific and Instruction-Following Knowledge

## Fáza A: bibliografická karta

- Názov: Layer-Aware Task Arithmetic: Disentangling Task-Specific and Instruction-Following Knowledge
- Autori: Yan-Lun Chen, Yi-Ru Wei, Chia-Yi Hsu, Chia-Mu Yu, Chun-Ying Huang, Ying-Dar Lin, Yu-Sung Wu, Wei-Bin Lee
- Rok: 2025
- Typ článku: Findings of EMNLP 2025 + arXiv
- Link: <https://arxiv.org/abs/2502.20186>
- Alternatívny link: <https://aclanthology.org/2025.findings-emnlp.644/>
- Téma: Task vectors / editing in weight space
- Relevancia pre DP: `5/5`

## 1) Motivácia autorov výskumu

Autori vychádzajú z toho, že `Task Arithmetic (TA)` je síce jednoduchý a účinný spôsob kombinovania task vectors, ale pri LLMs naráža na problém interferencie medzi úlohami. Pri merge viacerých fine-tuned modelov sa totiž často mieša skutočne task-specific knowledge s všeobecným `instruction-following` správaním, čo vedie k degradácii utility alebo k oslabeniu cieľových schopností.

Motiváciou článku je preto rozlíšiť, ktoré časti task vectora skutočne nesú task-specific informáciu a ktoré skôr zachytávajú všeobecné instruction-following správanie. Autori predpokladajú, že tieto zložky sú rozložené nerovnomerne naprieč vrstvami modelu, a preto treba pristúpiť k `layer-aware` merge stratégii.

## 2) Lokalizácia analýzy literatúry

Súvisiaca literatúra je rozptýlená medzi úvod, metodickú časť a porovnanie s baseline metódami.

Článok nadväzuje najmä na:

- pôvodný `Task Arithmetic`,
- `TIES`,
- `DARE`,
- `Safety Arithmetic`,
- a všeobecnú líniu model editing / model merging pre LLMs.

Na rozdiel od starších prác však autori neberú task vector ako homogénny objekt. Ich hlavný príspevok spočíva v tom, že analyzujú jeho vnútornú štruktúru po vrstvách a prepájajú ju s rozdielom medzi task-specific a instruction-following knowledge.

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je zlepšiť task arithmetic pri LLMs tak, aby sa pri merge lepšie zachovali cieľové schopnosti a zároveň sa minimalizovala degradácia všeobecnej utility modelu.

Hlavnú výskumnú otázku možno formulovať takto:

**Dá sa task arithmetic zlepšiť tým, že sa task vectors budú spracovávať po vrstvách a budú sa oddeľovať task-specific komponenty od všeobecného instruction-following signálu?**

S tým súvisia aj podotázky:

- ktoré vrstvy sú najviac zodpovedné za task-specific knowledge,
- ktoré vrstvy sú silnejšie previazané s instruction-following správaním,
- či odstránenie alebo oslabenie vysoko podobných vrstiev zníži interferenciu,
- či takýto prístup pomôže pri `task learning` aj `task forgetting`,
- či sa efekt prenáša aj na cross-lingual forgetting scenáre.

## 4) Koncepčný rámec článku

Koncepčný rámec článku je založený na rozlíšení dvoch typov znalostí v task vectors:

- `task-specific knowledge`,
- `instruction-following knowledge`.

Autori predpokladajú, že úplný task vector obsahuje zmes oboch komponentov. Preto zavádzajú `Layer-Aware Task Arithmetic (LATA)`, kde sa nepracuje s jedným globálnym task vectorom, ale s `layer vectors`.

Kľúčová idea LATA je:

- vytvoriť `instruction vector`,
- porovnať s ním layer-wise task / complex vectors,
- podľa similarity rankingov identifikovať vrstvy, ktoré sú príliš podobné instruction vectoru,
- a ponechať hlavne `pure vectors`, teda vrstvy viac viazané na cieľovú úlohu než na všeobecné inštrukčné správanie.

Pre Gemma-2-9b používajú variant `Linear-Drop-by-Rank`, pre Llama-3-8B variant `Logarithmic-Drop-by-Rank`, a pri forgettingu aj `Drop-with-Threshold`.

## 5) Použitá metóda (stratégia) výskumu

Ide o `experimentálny kvantitatívny výskum` s návrhom novej layer-wise editačnej metódy pre LLM task arithmetic.

Autori porovnávajú LATA s viacerými baseline metódami:

- `TA`,
- `TIES`,
- `DARE`,
- `DARE + TIES`,
- pri forgettingu aj `Safety Arithmetic`.

Metodika sa delí na dve hlavné časti:

- `task learning`, kde mergeujú fine-tuned modely pre úlohy ako `UA`, `Math` a `Code`,
- `task forgetting`, kde sa snažia odstrániť nežiaducu `unalignment` schopnosť pri zachovaní utility.

Podstatou metódy je layer-wise filtrovanie task vectorov podľa ich podobnosti s instruction vectorom. LATA tak dynamicky vyberá vrstvy, ktoré majú najväčší prínos pre cieľovú úlohu a najmenšiu interferenciu s globálnym instruction-following správaním modelu.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Autori používajú dve hlavné základné architektúry:

- `Gemma-2-9b`,
- `Llama-3-8B`.

Pre task learning pracujú s expert modelmi pre:

- `UA` / unalignment,
- `Math`,
- `Code`.

Konkrétne benchmarky a metriky zahŕňajú:

- `WikiText-2` pre utility a perplexity,
- `GSM8K` pre matematické schopnosti,
- `HumanEval` pre code generation,
- `GPT-4` scoring rizikovosti výstupu pre `UA`.

Pri task forgettingu navyše testujú cross-lingual scenáre na cieľových modeloch v jazykoch:

- tradičná čínština,
- japončina,
- nemčina,
- ruština,
- thajčina.

Používajú aj jazykovo špecifické eval sady ako `TMMLU+`, `JAQKET_v2`, `JSQuAD`, `JCommonsenseQA`, lokalizované verzie `SQuAD`, `TruthfulQA` a `NLI`.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Autori používajú `kvantitatívnu komparatívnu analýzu` a dopĺňajú ju o layer-wise interpretačnú analýzu.

Porovnávajú:

- utility po merge,
- výkon na cieľových taskoch,
- rizikovosť výstupu pri unalignment scenároch,
- správanie pri kombinácii 2 a 3 taskov,
- výsledky task forgettingu v rôznych jazykoch.

Na interpretačnej úrovni analyzujú:

- similarity rankings medzi layer vectors a instruction vectorom,
- rozloženie dôležitých vrstiev naprieč hĺbkou modelu,
- mieru overlapu medzi rôznymi taskami,
- a to, aký malý podiel vrstiev stačí na účinné odstránenie alebo pridanie schopnosti.

Takáto analýza umožňuje ukázať nielen to, že LATA funguje, ale aj prečo funguje lepšie než globálne task-vector baseline.

## Hlavné zistenia

Článok ukazuje, že layer-wise pohľad na task arithmetic výrazne pomáha pri LLM merge/editing scenároch.

Hlavné zistenia sú:

- `LATA` konzistentne dosahuje najlepšiu alebo veľmi konkurencieschopnú rovnováhu medzi utility a target-task výkonom,
- pri merge úloh ako `UA + Math`, `Math + Code` alebo `UA + Math + Code` prekonáva TA, TIES aj DARE na väčšine metrík,
- LATA typicky udržiava najnižšiu perplexitu na `WikiText-2`, teda najlepšie chráni všeobecnú utility modelu,
- pri forgettingu znižuje GPT-4 harm scores pod hodnotu `2` vo všetkých testovaných jazykoch, pričom utility ostáva približne na úrovni pôvodného modelu,
- len malá časť layer vectors je skutočne kritická pre cieľovú schopnosť; autori uvádzajú, že pri prahu `0.95` zostáva len približne `10 %` vrstiev ako `pure vectors`,
- neskoršie vrstvy, najmä približne po vrstve `20` a zvlášť okolo `26-30`, sa javia ako dôležitejšie pre task-specific správanie,
- vysoká podobnosť medzi math a code vectors naznačuje, že podobné tasky sa môžu pri spoločnom merge dokonca vzájomne posilňovať.

Najdôležitejšie zistenie pre DP je, že task vectors nie sú homogénne: ich význam sa výrazne mení po vrstvách a práve táto štruktúra môže rozhodovať o úspechu kombinácie znalostí.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Tento článok je pre DP veľmi silný, pretože prenáša task arithmetic z abstraktnej úrovne celého vektora na jemnejšiu `layer-wise` úroveň.

Jeho prínos pre tému `Kombinácia znalostí veľkých jazykových modelov` je vysoký, pretože:

- rieši priamo `LLM-specific task arithmetic`,
- ukazuje, že kombinácia znalostí vo weight space závisí od vrstvy a typu reprezentovanej znalosti,
- prepája merge/editing s otázkou disentanglingu medzi `instruction-following` a `task-specific` knowledge,
- ponúka praktickú metódu, ktorá vie súčasne zlepšiť merge aj selective forgetting,
- rozširuje task-vector literatúru smerom k jemnejšiemu, analytickejšiemu a použiteľnejšiemu frameworku.

Pre DP je to výborný moderný článok, lebo ukazuje, že ďalší pokrok v kombinovaní znalostí nemusí prísť len z lepších globálnych koeficientov, ale aj z rozpoznania, kde presne v modeli je daná znalosť reprezentovaná.

## Dôkazové citácie / oporné časti článku

- Motivácia:
  - "merging multiple fine-tuned models often leads to degraded performance due to overlapping instruction-following components"
  - "disentangling task-specific and instruction-following knowledge"

- Hlavná myšlienka:
  - "LATA leverages dynamic task representations"
  - "LATA precisely targets task vectors for removal"

- Layer-wise interpretácia:
  - "Layers with lower similarity (thus more impact on the target task) generally appear after layer 20, especially between layers 26 and 30."
  - "earlier layers focus more on processing the input instructions"
  - "later layers generate outputs ... causing parameter changes there to have greater impact on the target task"

- Malý počet kritických vrstiev:
  - "only about 10% of the layer vectors were retained as pure vectors"
  - "LATA successfully isolates these crucial and non-crucial segments from the task vector"

- Hlavné výsledky:
  - "LATA consistently outperforms existing methods"
  - "LATA yields the best utility performance and lowest perplexity on WikiText-2"
  - "reducing GPT-4 harm scores below 2 for all tested languages"

