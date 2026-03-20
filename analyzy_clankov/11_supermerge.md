# SuperMerge: An Approach For Gradient-Based Model Merging

## Fáza A: bibliografická karta

- Názov: SuperMerge: An Approach For Gradient-Based Model Merging
- Autori: Haoyu Yang, Zheng Zhang, Saket Sathe
- Rok: 2024
- Typ článku: arXiv preprint
- Link: <https://arxiv.org/abs/2412.10416>
- Alternatívny link: <https://openreview.net/forum?id=lIdc5DUplq>
- Téma: Combining fine-tuned checkpoints
- Relevancia pre DP: `4/5`

## 1) Motivácia autorov výskumu

Autori riešia praktický problém, keď už existuje niekoľko fine-tuned modelov pre staršie úlohy a treba k nim pridať nové schopnosti bez toho, aby sa celý model znovu fine-tunoval na všetkých dátach. Opakované fine-tuning kolá sú výpočtovo drahé aj pri `PEFT` prístupoch, a preto je model merging atraktívnou alternatívou.

Motiváciou článku je zároveň ukázať, že bežné training-free merge metódy majú stále skryté náklady a limity. Najmä používajú jediný globálny scaling hyperparameter `λ`, hoci rôzne vrstvy a rôzne tasky prispievajú k finálnemu modelu nerovnomerne.

## 2) Lokalizácia analýzy literatúry

Analýza literatúry je sústredená hlavne v sekciách `1 Introduction`, `2 Related Work` a `4 Background and Motivation`.

Autori prepájajú tri hlavné oblasti:

- `fine-tuning` a `PEFT` prístupy ako `LoRA`, `IA3`, adaptéry,
- `model merging` pri identickej architektúre a identickej inicializácii,
- a gradient-based / supervised prístupy k učeniu merge váh.

Ako hlavné baseline a referenčné smery uvádzajú:

- `Task Arithmetic`,
- `TIES`,
- `DARE`,
- `AdaMerging`,
- a v širšom pozadí aj `Model Soups`, `Git Re-Basin`, `OT Fusion`, `ZipIt`.

Ich kritika existujúcich prístupov je najmä tá, že sú príliš `layer-agnostic`, silno závislé od jedného `λ` a nevedia sa dobre prispôsobiť tomu, že rôzne vrstvy sa medzi taskmi menia rôzne silno.

## 3) Výskumná otázka / cieľ výskumu

Cieľ článku je navrhnúť merge metódu, ktorá dokáže systematicky spájať viacero fine-tuned modelov pre staré aj nové úlohy tak, aby výsledný model dosiahol výkon blízky plnému multitask fine-tuningu, ale s oveľa nižšími nákladmi.

Hlavnú výskumnú otázku možno sformulovať takto:

**Dá sa zlepšiť merge viacerých fine-tuned modelov tým, že sa namiesto jedného globálneho scaling faktora naučia layer-wise príspevky jednotlivých task vectorov pomocou malej validačnej množiny?**

Vedľajšie otázky sú:

- či takýto gradient-based merge prekoná `Task Arithmetic`, `TIES`, `DARE` a `AdaMerging`,
- či bude fungovať pre generatívne aj prediktívne úlohy,
- a či sa dá spraviť pamäťovo úspornejší hierarchický variant bez výraznej straty výkonu.

## 4) Koncepčný rámec článku

Koncepčný rámec stojí na `task vectors`, teda rozdiele medzi fine-tuned modelom a base modelom. Namiesto toho, aby sa všetky task vectory škálovali jedným spoločným `λ`, autori zavádzajú samostatnú váhu `w(i, j)` pre task `i` a vrstvu `j`.

Merged model je zostavený tak, že pre každú vrstvu kombinuje base váhy s váženým súčtom layer-wise task vectorov. Váhy pre merge sa učia gradient descentom na malej validačnej množine, pričom sa používá `tanh`, aby ostali v intervale `(-1, 1)`.

Tento rámec teda predpokladá, že:

- dôležitosť vrstiev nie je rovnaká,
- rôzne tasky zasahujú rôzne vrstvy s rôznou intenzitou,
- a merge má byť `adaptive` na úrovni vrstiev, nie len na úrovni celého modelu.

Dôležitou súčasťou rámca je aj `hierarchical merging`, kde sa modely nespájajú všetky naraz, ale po menších skupinách cez stromovú štruktúru, aby sa znížili pamäťové nároky.

## 5) Použitá metóda (stratégia) výskumu

Ide o `empiricko-metodický` článok s návrhom novej merge metódy.

Autori navrhujú `SUPERMERGE`, kde:

- z každého fine-tuned modelu vypočítajú `task vector`,
- pre každú vrstvu a task zavedú trénovateľnú merge váhu,
- výsledný merged model skladajú layer-wise,
- a merge váhy optimalizujú gradient descentom na `Dval`.

Formálne má merge tvar:

- `theta_m(j) = theta_p(j) + sum_i tanh(w(i, j)) * tau(i, j)`

kde `tau(i, j)` je layer-wise task vector.

Metóda je `supervised`, ale veľmi ľahká, pretože netrénuje parametre základného modelu, iba malú maticu merge váh. Pre veľký počet modelov autori navrhujú aj `hierarchical SUPERMERGE`, ktoré postupuje po úrovniach stromu a zlučuje len menšie skupiny modelov naraz.

## 6) Metóda získania dát na vyhodnotenie výsledkov

Autori testujú metódu na dvoch typoch úloh:

- `generative NLP tasks`,
- `predictive CV tasks`.

Pre generatívne NLP používajú `T0-3B` ako base model a `IA3` ako PEFT mechanizmus. Fine-tuned modely vytvárajú na 11 datasetoch:

- `COPA`,
- `H-SWAG`,
- `Story Cloze`,
- `ANLI`,
- `CB`,
- `RTE`,
- `WSC`,
- `Winogrande`,
- `WiC`.

Pre out-of-domain generalizáciu používajú ďalšie unseen datasety:

- `PAWS`,
- `ROPES`,
- `Cosmos QA`,
- `Social IQA`,
- `QuAIL`,
- `QASC`,
- `WikiQA`,
- `QuaRTz`.

Pre vision experimenty používajú `ViT-B/32` v `CLIP` a osem image classification datasetov:

- `Cars`,
- `DTD`,
- `EuroSAT`,
- `GTSRB`,
- `MNIST`,
- `RESISC45`,
- `SUN397`,
- `SVHN`.

Pri niektorých datasetoch bez validačnej časti vytvárajú validation split z tréningových dát. Dôležité je, že samotné merge váhy sa učia len na malej validačnej množine; napríklad pri generatívnych NLP úlohách má validačný set spolu iba `32` príkladov.

## 7) Metóda analýzy dát na vyhodnotenie výsledkov

Vyhodnotenie je založené na benchmarkovom porovnaní s baseline metódami:

- `Task Arithmetic`,
- `DARE`,
- `TIES`,
- `AdaMerging`,
- individuálne fine-tuned modely,
- plný multitask fine-tuned model.

Autori sledujú:

- `in-domain` výkon,
- `out-of-domain` generalizáciu,
- priemernú `accuracy`,
- `rank` a `average rank`,
- výpočtové náklady,
- počet trénovateľných parametrov,
- a `peak memory`.

Okrem hlavného porovnania robia aj analytické doplnky:

- abláciu bez `tanh`,
- vizualizáciu learned merge weights naprieč vrstvami,
- porovnanie hustoty a rozloženia merge váh oproti `AdaMerging`,
- a analýzu výpočtových a pamäťových nárokov pre štandardný a hierarchický variant.

## Hlavné zistenia

Článok ukazuje, že `SUPERMERGE` konzistentne prekonáva bežné merge baseline na širokom spektre úloh.

Hlavné zistenia:

- `SUPERMERGE` dosahuje najlepší priemerný výkon aj priemerný rank na generatívnych NLP aj prediktívnych vision úlohách,
- pri generatívnych úlohách je obzvlášť silný na `COPA`, `H-SWAG` a `Story Cloze`,
- na unseen / out-of-domain NLP úlohách je stabilný a má najlepší priemerný výkon medzi baseline metódami,
- `DARE+TIES` generalizuje slabo, čo autori interpretujú ako dôsledok príliš agresívneho sparsification,
- `SUPERMERGE` potrebuje len `2112` trénovateľných parametrov pri merge 11 modelov,
- oproti plnému fine-tuningu používa približne `1000x` menej FLOPs,
- štandardný variant má vysoké pamäťové nároky kvôli držaniu task vectorov, ale `hierarchical SUPERMERGE` znižuje peak memory z `130.4 GB` na `32.7 GB`,
- obmedzenie merge váh cez `tanh` zlepšuje generalizáciu; bez neho výkon citeľne klesá,
- learned merge weights potvrdzujú, že hlbšie vrstvy a rôzne typy blokov sa správajú odlišne, takže layer-wise weighting je opodstatnený.

## Stručné zhodnotenie vhodnosti článku pre tému diplomovej práce

Článok je pre DP veľmi relevantný, pretože rieši priamo kombinovanie znalostí vo weight space cez spájanie viacerých fine-tuned checkpointov.

Je prínosný najmä tým, že:

- ide nad rámec jednoduchého `Task Arithmetic`,
- ukazuje, že merge môže byť `supervised` a pritom stále extrémne ľahký,
- zavádza `layer-wise` učené váhy namiesto jedného globálneho faktora,
- porovnáva generatívne aj prediktívne úlohy,
- a rieši aj praktický problém pamäte cez hierarchický merge.

Pre diplomovú prácu je to silný zdroj k línii `combining fine-tuned checkpoints`, najmä pri porovnaní training-free heuristík s ľahko učenými merge mechanizmami.

## Dôkazové citácie / oporné časti článku

- Motivácia:
  - "A straightforward solution requires fine-tuning the model again for both existing and new tasks, which is computationally expensive and time-consuming."
  - "existing model merging techniques such as DARE, TIES, and Task-Arithmetic have several unresolved challenges."

- Kľúčová myšlienka:
  - "SUPERMERGE is a supervised model merging approach that learns the contribution of each layer using a tiny amount of validation data"
  - "SUPERMERGE introduces a tiny number of trainable parameters"

- Koncepčný prínos:
  - "the layer-wise distribution of task vectors could vary significantly across different layers and tasks"
  - "This observation demonstrates the limitations of employing a universal hyperparameter"

- Výsledky:
  - "SUPERMERGE achieves the best performance in terms of both average accuracy and average rank."
  - "Table 2 shows that SUPERMERGE is robust when applied to out-of-domain tasks"

- Efektivita:
  - "SUPERMERGE displays a significant advantage with only 2,112 trainable parameters"
  - "Compared to fine-tuning methods, SUPERMERGE requires 1000x lower FLOPs."

- Hierarchický variant:
  - "Hierarchical merging significantly reduces the peak memory requirement from 130.4 GB to 32.7 GB."

