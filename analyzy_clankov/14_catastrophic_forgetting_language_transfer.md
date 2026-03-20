# Mitigating Catastrophic Forgetting in Language Transfer via Model Merging

## Fáza A: bibliografická karta

- Názov: Mitigating Catastrophic Forgetting in Language Transfer via Model Merging
- Autori: Anton Alexandrov, Veselin Raychev, Mark Niklas Muller, Ce Zhang, Martin Vechev, Kristina Toutanova
- Rok: 2024
- Typ článku: arXiv preprint
- Link: <https://arxiv.org/abs/2407.08699>
- Téma: Catastrophic forgetting vs transfer
- Relevancia pre DP: `5/5`

## 1) Motivácia autorov výskumu

Autori riešia veľmi praktický problém jazykového transferu otvorených LLM z prevažne anglického prostredia do iných jazykov. Pri takejto adaptácii model získa schopnosti v cieľovom jazyku, ale zároveň často výrazne zabúda na schopnosti pôvodného modelu, najmä na source-language znalosti a všeobecné reasoning či coding capability.

Motiváciou článku je nájsť tréningový postup, ktorý zlepší prenos do nového jazyka bez výrazného `catastrophic forgetting`. Autori tvrdia, že nestačí len experience replay alebo zníženie learning rate, ale treba lepšie vyvážiť veľkosť weight update-ov a kvalitu získaného task vectoru.

## 2) Lokalizácia analýzy literatúry

Analýza literatúry je rozptýlená najmä medzi sekcie `1 Introduction`, `2 Model Merging` a `7 Related work`.

Autori prepájajú tri línie výskumu:

- `catastrophic forgetting` pri continued pretraining a instruction tuning,
- `experience replay` ako klasickú obranu proti forgettingu,
- a `model merging` ako mechanizmus kombinovania task-specific update-ov.

Ako relevantné východiská spomínajú:

- prístupy so source-language replay,
- zníženie learning rate,
- `LoRA`,
- merge metódy ako `LINEAR`, `SLERP` a `MODELSTOCK`.

Ich vlastný príspevok spočíva v tom, že model merging nepoužívajú len ako záverečný post-hoc krok, ale ako integrálnu súčasť tréningovej stratégie pre jazykový transfer.

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je navrhnúť tréningovú metódu pre language transfer, ktorá zníži forgetting source-domain schopností a zároveň zachová alebo zlepší výkon v cieľovom jazyku.

Hlavnú výskumnú otázku možno sformulovať takto:

**Dá sa jazyková adaptácia LLM spraviť tak, aby weight updates boli menšie, kvalitnejšie a menej deštruktívne, ak sa tréning rozdelí na vetvy a tie sa priebežne merge-nú?**

Vedľajšie otázky sú:

- ako súvisí magnitúda weight change s forgettingom,
- či iteratívny merge zlepší trade-off medzi learning a forgetting,
- ako sa metóda správa pri continued pretraining aj instruction tuning,
- a či funguje pre jazyky s odlišnou podobnosťou k angličtine.

## 4) Koncepčný rámec článku

Koncepčný rámec článku stojí na dvoch intuíciách:

- menšie task vectory vedú k menšiemu forgettingu, ale obyčajne aj k menšiemu learningu,
- pri fine-tuningu vzniká v task vectoroch náhodná zložka, takže priemerovanie viacerých update-ov môže znížiť šum a zvýšiť ich kvalitu.

Na tom je postavený `Branch-and-Merge (BAM)`:

- tréningové dáta sa rozdelia na `N` slices,
- aktuálny base model sa paralelne trénuje na `K` slices,
- vzniknuté modely sa merge-nú,
- merged model sa stane základom ďalšej iterácie.

Koncepčne teda ide o iteratívne striedanie:

- lokálneho učenia na podmnožinách dát,
- a merge kroku, ktorý stabilizuje weight updates.

Autori tvrdia, že tým vznikajú `lower magnitude but higher quality weight changes`, čo je kľúčový mechanizmus znižovania forgettingu.

## 5) Použitá metóda (stratégia) výskumu

Ide o `metodicko-empirický` článok s návrhom novej tréningovej stratégie.

Metóda `BAM` funguje takto:

1. tréningové dáta sa rozdelia na `N` častí,
2. v každom kroku sa z aktuálneho base modelu vytvorí `K` paralelných vetiev,
3. každá vetva sa trénuje na inom data slice,
4. následne sa modely merge-nú s koeficientom `c` a pokračuje sa ďalšou iteráciou.

Autori experimentujú najmä s `K = 2` a používajú merge metódy:

- `LINEAR`,
- `SLERP`,
- `MODELSTOCK`.

V ďalšej vrstve metodiky dopĺňajú aj `approximate experience replay`, teda carefully curated zdrojové anglické dáta, aby sa transfer do nového jazyka neopieral len o cieľový jazyk. Skúmajú pritom continued pretraining aj instruction tuning.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Autori testujú metódu na transfere:

- z angličtiny do `Bulgarian`,
- z angličtiny do `German`.

Použité base modely:

- `MISTRAL-7B`,
- `LLAMA-3-8B`.

Vyhodnotenie robia pre dva adaptačné režimy:

- `continued pretraining (CPT)`,
- `instruction fine-tuning (IFT)`.

Pre source-domain replay vytvárajú `approximate experience replay` mix z:

- `OpenWebText`,
- anglickej Wikipédie,
- GitHub repozitárov,
- a instruction datasets ako `OpenHermes`, `SlimOrca`, `MetaMathQA`, `CodeInstructions`.

Pre bulharčinu zostavujú rozsiahly mix z:

- webových dát,
- Wikipédie,
- právnych textov,
- literatúry,
- transcriptov,
- a preložených instruction datasets.

Pre nemčinu používajú veľký výber z `CulturaX` a nemecké IFT datasety.

Benchmarky pokrývajú oba smery:

- anglické benchmarky ako `HellaSwag`, `Winogrande`, `ARC`, `MMLU`, `GSM8K`, `MathQA`, `TriviaQA`, `BeleBele`,
- nemecké benchmarky z `LM Evaluation Harness`,
- a vlastnú širšiu evaluačnú sadu pre bulharčinu.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Analýza kombinuje:

- benchmarkové porovnanie výkonu v target aj source jazyku,
- sledovanie `negative log-likelihood` ako aproximácie forgettingu,
- a ablačné experimenty nad hyperparametrami `N`, `K`, typom merging-u a kvalitou replay dát.

Autori sledujú najmä:

- `Avg BG` alebo `Avg DE` ako learning v cieľovom jazyku,
- `Avg EN` ako zachovanie source-domain capability,
- `BG NLL` a `EN NLL`,
- L2 magnitúdu zmeny váh,
- a dynamiku učenia počas tréningu.

Silný analytický moment článku je ukázanie, že:

- forgetting a learning úzko korelujú s magnitúdou weight change,
- ale `BAM` je efektívnejší než štandardný tréning, lebo pri rovnakej alebo menšej zmene váh dosahuje lepší trade-off.

## Hlavné zistenia

Článok ukazuje, že `BAM` konzistentne znižuje forgetting pri jazykovej adaptácii a pritom zachováva alebo zlepšuje výkon v cieľovom jazyku.

Hlavné zistenia sú:

- pri transferoch do bulharčiny aj nemčiny `BAM` zlepšuje trade-off medzi target-language learning a source-language retention oproti štandardnému continued pretrainingu,
- pri `MISTRAL-7B` pre bulharčinu `BAM` pri i.i.d. slices takmer dorovná standard CPT v bulharčine, ale výrazne znižuje forgetting a zlepšuje anglické benchmarky,
- pri `LLAMA-3-8B` pre bulharčinu aj nemčinu `BAM` zlepšuje priemerné skóre v cieľovom aj zdrojovom jazyku,
- pri instruction tuningu je efekt ešte výraznejší; `BAM BG | EN` prekonáva `LLAMA-3-8B-Instruct` v bulharčine aj angličtine,
- approximate experience replay je výrazne lepší než minimal replay a ďalej znižuje forgetting,
- zníženie learning rate síce zníži forgetting, ale príliš obmedzí learning; `BAM` dosahuje lepší kompromis,
- `LoRA` je v tomto settingu ešte konzervatívnejšia a vedie k slabšiemu learningu,
- magnitúda weight change silno koreluje s forgettingom, no `BAM` pri danej zmene váh dosahuje viac learningu a menej forgettingu,
- medzi merge metódami sú `SLERP` a `LINEAR` veľmi podobné, s miernou výhodou `SLERP`,
- najlepší trade-off dosahuje pri ich experimentoch paralelizácia `K = 2`.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Tento článok je pre DP mimoriadne cenný, pretože veľmi priamo prepája model merging s témou `catastrophic forgetting vs transfer`.

Je dôležitý najmä tým, že:

- používa merge ako mechanizmus regulácie učenia, nie len ako záverečné spojenie checkpointov,
- ukazuje, že kvalita a magnitúda task vectorov sú kľúčové pre zachovanie starých znalostí,
- analyzuje practical trade-off medzi target adaptation a retention,
- pokrýva aj continued pretraining aj instruction tuning,
- a rozširuje tému weight-level knowledge combination do cross-lingual adaptation.

Pre diplomovú prácu je to veľmi silný článok, pretože ukazuje, že merge môže slúžiť nielen na kombináciu viacerých skillov, ale aj na kontrolu zabúdania počas transferu.

## Dôkazové citácie / oporné časti článku

- Motivácia:
  - "language adaptation is often accompanied by catastrophic forgetting of the base model's capabilities"
  - "math and coding skills learned in English can be extremely helpful for general problem-solving or reasoning tasks in other languages"

- Hlavná myšlienka:
  - "We address this issue by proposing Branch-and-Merge (BAM)"
  - "this yields lower magnitude but higher quality weight changes, reducing forgetting of the source domain while maintaining learning on the target domain"

- Mechanizmus:
  - "lower magnitude weight changes ... lead to less forgetting but also less learning"
  - "We can thus reduce forgetting by reducing the task vector magnitude while offsetting the reduced learning by increasing task vector quality"

- Výsledky:
  - "BAM can significantly reduce forgetting while matching or even improving target domain performance"
  - "BAM consistently improves benchmark performance in both the target and source language compared to standard training"
  - "outperform LLAMA-3-8B-Instruct not only in Bulgarian (by 10.9%) but also in English (by 1.3%)"

- Analytický záver:
  - "both forgetting and learning strongly correlated with weight change magnitude"
  - "BAM is more efficient, i.e., yields more learning and less forgetting at the same weight change"

