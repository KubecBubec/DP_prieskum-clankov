# Model soups: averaging weights of multiple fine-tuned models improves accuracy without increasing inference time

## Fáza A: bibliografická karta

- Názov: Model soups: averaging weights of multiple fine-tuned models improves accuracy without increasing inference time
- Autori: Mitchell Wortsman, Gabriel Ilharco, Samir Yitzhak Gadre, Rebecca Roelofs, Raphael Gontijo-Lopes, Ari S. Morcos, Hongseok Namkoong, Ali Farhadi, Yair Carmon, Simon Kornblith, Ludwig Schmidt
- Rok: 2022
- Typ článku: ICML 2022 / PMLR + arXiv
- Link: <https://arxiv.org/abs/2203.05482>
- Téma: Model merging / model soups
- Relevancia pre DP: `5/5`

## 1) Motivácia autorov výskumu

Autori vychádzajú z bežného postupu fine-tuningu veľkých predtrénovaných modelov: vykoná sa hyperparametrický sweep, následne sa vyberie jeden model s najlepšou validačnou presnosťou a ostatné modely sa zahodia. V článku ukazujú, že tento postup má dve zásadné nevýhody. Po prvé, zahadzujú sa potenciálne užitočné riešenia, hoci ich ensembling by mohol zlepšiť presnosť. Po druhé, ensembling zvyšuje výpočtové a pamäťové náklady pri inferencii. Autori preto hľadajú alternatívu, ktorá by zachovala výhody kombinácie viacerých modelov, ale bez dodatočných nákladov počas nasadenia.

Ďalšou motiváciou je pozorovanie, že fine-tuned modely zo spoločnej predtrénovanej inicializácie sa často nachádzajú v jednej nízkochybovej oblasti loss landscape. To vytvára predpoklad, že priemerovanie ich váh nemusí viesť k deštrukcii výkonu, ale naopak k jeho zlepšeniu.

## 2) Lokalizácia analýzy literatúry

Analýza súvisiacej literatúry sa nachádza najmä v samostatnej sekcii `6. Related work`. Už v úvode sa však stručne objavujú odkazy na práce o pretraining/fine-tuningu, averagingu váh a ensemblingu, ktoré rámcujú problém.

Sekcia `Related work` je tematicky rozdelená na tri časti:

- `Averaging model weights`
- `Pre-training and fine-tuning`
- `Ensembles`

To je pre túto úlohu ideálna related work sekcia, pretože priamo lokalizuje teoretické a metodické východiská článku.

## 3) Výskumná otázka / cieľ výskumu

Cieľ výskumu je overiť, či môže byť priemerovanie váh viacerých nezávisle fine-tuned modelov lepšou alternatívou než výber jediného najlepšieho modelu z hyperparametrického sweepu.

Hlavnú výskumnú otázku možno formulovať takto:

**Dokáže averaging váh viacerých fine-tuned modelov zlepšiť presnosť a robustnosť oproti najlepšiemu individuálnemu modelu bez zvýšenia nákladov pri inferencii?**

S tým súvisia aj čiastkové otázky:

- kedy uniform averaging funguje a kedy zlyháva,
- či greedy selection vedie k lepšiemu merged modelu než jednoduchý uniform soup,
- ako blízko sa soups dostávajú k logit ensemblingu,
- aký je vzťah medzi úspechom soups, plochosťou loss landscape a istotou predikcií.

## 4) Koncepčný rámec článku

Koncepčný rámec článku stojí na štyroch hlavných ideách:

- **transfer learning / fine-tuning veľkých predtrénovaných modelov**, kde sa vychádza z modelov ako CLIP, ALIGN alebo ViT-G,
- **loss landscape / low error basin hypotéza**, podľa ktorej sa fine-tuned modely zo spoločnej inicializácie môžu nachádzať v jednej oblasti s nízkou chybou,
- **weight-space model merging**, teda kombinovanie modelov cez averaging parametrov namiesto kombinovania výstupov,
- **porovnanie so štandardnými baseline prístupmi**, najmä s výberom najlepšieho modelu a s ensemblingom.

V tomto rámci sú `model soups` chápané ako praktická stratégia, ktorá využíva už existujúce fine-tuned riešenia a mení ich z "odpadu po sweepe" na jeden lepší model.

## 5) Použitá metóda (stratégia) výskumu

Ide o **experimentálny kvantitatívny výskum** založený na porovnávacích experimentoch.

Autori najprv vykonávajú fine-tuning veľkých predtrénovaných modelov s rôznymi hyperparametrickými konfiguráciami. Následne z takto získaných checkpointov vytvárajú viacero merge variantov:

- `uniform soup` - priemerovanie všetkých kandidátnych modelov,
- `greedy soup` - sekvenčné pridávanie modelov iba vtedy, ak sa zlepší výkon na held-out validačnej množine,
- `learned soup` - optimalizácia interpolačných váh gradientovo.

Tieto prístupy porovnávajú proti baseline metódam:

- `best individual model`,
- `ensemble`,
- `greedy ensemble`,
- ďalšie averaging baseline varianty v prílohách.

Výskumná stratégia teda kombinuje:

- hyperparameter sweep,
- konštrukciu merged modelov,
- benchmarkové porovnanie výkonnosti,
- teoreticko-analytické porovnanie soups a ensemblingu.

## 6) Metóda získania dát na vyhodnotenie výsledkov

V článku nejde o zber dát od ľudí, ale o **experimentálne získanie metrík výkonu modelov na benchmarkových datasetoch**.

Autori fine-tunujú a vyhodnocujú modely na viacerých typoch úloh a datasetov:

- hlavné porovnania prebiehajú na `ImageNet`,
- robustnosť posudzujú na prirodzených distribučných posunoch: `ImageNetV2`, `ImageNet-R`, `ImageNet-Sketch`, `ObjectNet`, `ImageNet-A`,
- doplnkovo používajú aj `ReaL` a `multilabel` varianty ImageNet validácie,
- rozšírenie mimo vision domény testujú aj na text classification úlohách z `GLUE` (`MRPC`, `RTE`, `CoLA`, `SST-2`),
- v doplnkových experimentoch aj na `WILDS`.

Metódou získania dát je teda:

- spustenie fine-tuning behov,
- záznam validačných a testovacích metrík,
- zber výsledkov merged modelov a baseline modelov na rovnakých benchmarkoch.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Autori používajú **kvantitatívnu komparatívnu analýzu** výsledkov.

Konkrétne analyzujú:

- rozdiely medzi `best individual model`, `uniform soup`, `greedy soup`, `learned soup` a `ensemble`,
- výkon na in-distribution dátach aj na distribučných posunoch,
- vplyv hyperparametrov a geometrie riešení v priestore váh,
- vzťah medzi averagingom váh a logit ensemblingom.

Na interpretáciu používajú:

- tabuľky s presnosťou na benchmarkoch,
- grafy a vizualizácie error/loss landscape,
- analytický model založený na cross-entropy surrogate loss, ktorým porovnávajú `soup` a `ensemble`.

Primárne metriky sú najmä:

- `top-1 accuracy`,
- agregovaná presnosť na distribution shift datasetoch,
- task-specific metriky pri NLP úlohách (napr. accuracy, F1, Matthews correlation).

## Hlavné zistenia

Hlavným zistením článku je, že averaging váh viacerých fine-tuned modelov často vedie k lepšiemu modelu než výber jediného najlepšieho checkpointu.

Kľúčové výsledky:

- `greedy soup` často prekonáva najlepší individuálny model pri zachovaní `O(1)` inferenčných nákladov,
- výsledný ViT-G greedy soup dosiahol `90.94 %` top-1 accuracy na ImageNet,
- soups zlepšujú aj výkonnosť pri distribučnom posune,
- v mnohých nastaveniach sa soups približujú výkonu ensemblingu bez jeho nákladov,
- prístup sa neobmedzuje len na vision, ale ukazuje sľubné výsledky aj pri NLP text classification úlohách,
- greedy soup je robustnejší než uniform soup, pretože vie vynechať modely, ktoré by priemer poškodili.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Článok je pre tému diplomovej práce veľmi vhodný, pretože poskytuje jeden z najdôležitejších základov pre uvažovanie o kombinácii znalostí na úrovni váh modelu.

Pre DP je dôležitý najmä preto, že:

- explicitne ukazuje, že kombinácia viacerých modelov môže prebiehať priamo vo `weight space`,
- zavádza praktický merge postup bez potreby dodatočného tréningu,
- prepája empirický úspech s teoretickou interpretáciou v priestore váh,
- vytvára základ pre neskoršie práce o task vectors, checkpoint merging a interference-aware merging.

Obmedzením vzhľadom na DP je, že hlavné experimenty sú prevažne vo vizuálnej doméne. Napriek tomu je metodologický princíp priamo relevantný pre kombináciu znalostí veľkých jazykových modelov.

## Dôkazové citácie / oporné časti článku

- Motivácia problému:
  - "Typically, the fine-tuning process involves two steps: (1) fine-tune models with a variety of hyperparameter configurations, and (2) select the model which achieves the highest accuracy on the held-out validation set. The remaining models are then discarded."
  - "Selecting a single model and discarding the rest has several downsides."

- Hlavný cieľ:
  - "Instead of selecting the individual fine-tuned model which achieves the highest accuracy on the held-out validation set, we average the weights of models fine-tuned independently, and refer to the result as a model soup."
  - "averaging several of these models to form a model soup requires no additional training and adds no cost at inference time."

- Koncepčný rámec:
  - "fine-tuned models optimized independently from the same pre-trained initialization lie in the same basin of the error landscape, inspiring our method."
  - "Our approach extends weight averaging to the context of fine-tuning, where we find that it also works across many independent runs with varied hyperparemeter configurations."

- Metóda:
  - "This section highlights three recipes for model souping, the uniform, greedy, and learned soup, though the greedy soup is our central method."
  - "The greedy soup is constructed by sequentially adding each model as a potential ingredient in the soup, and only keeping the model in the soup if performance on a held out validation set ... improves."

- Dáta a benchmarky:
  - "The primary models we fine-tune are the CLIP ..., ALIGN ..., a ViT-G/14 model pre-trained on JFT-3B ..., and transformer models for text classification."
  - "When fine-tuning on ImageNet we also evaluate on the five natural distribution shifts: ImageNetV2, ImageNet-R, ImageNet-Sketch, ObjectNet, and ImageNet-A."

- Hlavné zistenia:
  - "The resulting ViT-G model, which attains 90.94% top-1 accuracy on ImageNet, achieved a new state of the art."
  - "Our results challenge the conventional procedure of selecting the best model on the held-out validation set when finetuning."

