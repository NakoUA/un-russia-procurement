# UN procurement from Russian suppliers, 2015–2025

This repository publishes one dataset and the charts built from it: the value of the contracts UN agencies placed with suppliers registered in the Russian Federation between 2015 and 2025.

The underlying figures are already public. The UN publishes them through its procurement dashboard and its Annual Statistical Report. What this repository adds is convenience: a single flat file you can download, sort, filter and check, instead of a dashboard you have to click through.

**Status: work in progress.** The charts live on one page: [`index.html`](index.html).

## The data

`data/russia-suppliers_2015-2025.csv` — 4,110 rows, 530,318 bytes, one row per contract line.

| Column | What it holds |
| --- | --- |
| `year` | reporting year (2015–2025) |
| `organization` | the UN agency that paid (24 of them) |
| `supplier_country` | always `Russian Federation` — this is how the slice is defined |
| `supplier_name` | the supplier as spelled in the source (346 distinct spellings) |
| `category` | one of 47 broad procurement categories |
| `value_usd` | value in US dollars |
| `reference` | contract identifier (3,418 distinct) |

The 47 categories are the UN's own, drawn from the UNSPSC classification, and they are not equally fine-grained. Most organizations report at the second level of that hierarchy, some only at the top level, and where an organization cannot classify a purchase at all it is reported as unspecified goods or services ([the UN's own explanation of this data](https://www.ungm.org/Shared/KnowledgeCenter/Pages/asr_about)).

## Four things to know before you count

**1. One row is not one contract.** There are 4,110 rows but 3,418 contracts: 654 contracts are split across two to six rows, one per category, and 1,346 rows are fragments of a split contract. Counting rows overstates the number of contracts by about 20%. Group by `reference` first.

**2. Supplier names are not standardised, and consolidating them gives an upper bound, not a count.** The file carries no supplier identifier: no registration number, no vendor code, nothing that survives a change in spelling. The name is the only handle there is. With an identifier, counting companies is arithmetic; without one, it is a judgement about text, and a judgement can only give an upper bound.

Contracts in this file do have one: `reference` is a contract identifier, which is why the number of contracts above is exact and the number of companies below is not.

The same firm appears under several spellings (346 distinct strings in this file), so a search by name will silently miss rows. Consolidated, those 346 spellings come to roughly **296 groups**, and that figure is the most we can honestly say: the true number of firms is *no higher* than 296, and otherwise unknown.

One of those misses is invisible to the eye. Row 102 reads `DRANZHEVSKIY MAKSIM DMITRIEVICH INDIVIDUAL ЕNTREPRENEUR`, and the E that opens ENTREPRENEUR is not the Latin E (U+0045) but the Cyrillic Е (U+0415). On screen the two are the same shape. Five other rows carry the phrase in Latin, so a search for INDIVIDUAL ENTREPRENEUR returns five of the six. This is how the name arrived from the source, and it is left exactly as it arrived: the file is published byte for byte, so nothing here is silently corrected. If you search this file by name, search for fragments rather than whole phrases.

The consolidation has two layers. A strict mechanical rule merges only spellings that become identical after normalisation (quotes, commas and legal forms stripped), taking 346 down to 309. On top of it, thirty hand-written patterns finish what the rule could not: a name truncated at about 34 characters, a different legal form, a different transliteration. That takes 309 to 296, and the whole difference is thirteen rows in five groups.

Those patterns were written for the large firms at the top of the table. The tail (266 of the 296 groups) was never reviewed by hand, so small suppliers scattered across spellings are almost certainly still counted as several groups each. That is why 296 is a ceiling rather than a tally.

Four of the 296 name nobody at all, and they are anonymous for two different reasons.

`(Unspecified supplier)` and `(Name withheld)` are identities the source had and did not publish. In the file itself they appear as `UNSPECIFIED SUPPLIER`, `NAME WITHHELD FOR PRIVACY REASONS` and `NAME WITHHELD FOR SECURITY REASONS`, the last two being two different grounds for withholding.

`INDIVIDUAL SUPPLIER` and `INDIVIDUAL SERVICE CONTRACTOR` are a different matter: nothing was withheld, because the source does not name this class of counterparty (a natural person contracted as such) in the first place.

Together those five strings cover 208 rows and $183.3m, standing in for an unknown number of real suppliers, and filtering on one of them misses the other four.

Where you need named suppliers, the figure is **292 at most**.

**3. In 2025 the country field in the source stopped being reliable.**

Normally one supplier belongs to one country. In 2025 the same supplier name often turns up under two or three different countries — it happens to about one supplier in ten. No other year comes close: the next highest is 2016 at 3.3%, and most years sit below 1%.

That matters here in two opposite directions. A row may have been pulled into this file that does not belong in it, because the source labelled a non-Russian supplier as Russian. And a genuinely Russian row may be sitting under some other country, and so be missing from this file altogether.

You cannot check this in the file itself. Here `supplier_country` is `Russian Federation` in every row by construction, so there is nothing to compare. The instability is only visible across the whole corpus, where every country is present at once:

| Year | Suppliers under more than one country | Suppliers that year | Share |
| --- | ---: | ---: | ---: |
| 2015 | 205 | 19,767 | 1.04% |
| 2016 | 747 | 22,650 | 3.30% |
| 2017 | 344 | 22,464 | 1.53% |
| 2018 | 170 | 23,173 | 0.73% |
| 2019 | 181 | 24,102 | 0.75% |
| 2020 | 193 | 25,355 | 0.76% |
| 2021 | 210 | 28,162 | 0.75% |
| 2022 | 177 | 29,948 | 0.59% |
| 2023 | 182 | 28,667 | 0.63% |
| 2024 | 309 | 28,903 | 1.07% |
| **2025** | **2,890** | **27,885** | **10.36%** |

Measured over the full dashboard export (994,900 rows covering every supplier country for 2015–2025) by counting, for each supplier name, how many distinct countries it appears under in that year.

That full corpus comes from the UN's own [procurement by supplier page](https://www.ungm.org/Shared/KnowledgeCenter/Pages/asr_data_supplier).

Placeholders for withheld names were excluded from the count: by their nature they appear under many countries at once, and leaving them in would inflate every year.

One limit worth stating plainly: this measures the field, not the row. A supplier appearing under several countries is evidence that the column was unreliable that year. It is not evidence that any particular row in this file is wrong.

**4. Every total is a floor.** Only contracts at or above 30,000 USD are published at source: the smallest contract in this file is exactly $30,000.00. Individual rows can be smaller, 674 of them are, because they are fragments of larger contracts.

**How much smaller this is than the UN's own totals.** The UN's Annual Statistical Report counts every purchase its organizations make. This dashboard carries only contracts at or above $30,000. Over 2015–2025 that leaves **19.2% less money here** than in the reports: $199.8 billion against $247.4 billion. In eight of the eleven years the dashboard holds between 82% and 91% of the report, and those eight are every year except 2021–2023: taken together their shortfall is 15.2%, and no single one of them falls outside 9.5% to 17.8%. Those three years are wider: taken together, 27.2%, for a separate and documented reason: COVID-19 vaccine procurement was excluded from the detailed publication, which the 2021 report states plainly as "cannot be published in the report or dashboards for confidentiality reasons".

| Year | UN's own total | In the dashboard | Coverage | Shortfall |
| ---: | ---: | ---: | ---: | ---: |
| 2015 | $17,575m | $15,902.5m | 90.5% | 9.5% |
| 2016 | $17,713m | $15,038.0m | 84.9% | 15.1% |
| 2017 | $18,623m | $15,881.0m | 85.3% | 14.7% |
| 2018 | $18,785m | $15,890.5m | 84.6% | 15.4% |
| 2019 | $19,893m | $16,349.7m | 82.2% | 17.8% |
| 2020 | $22,338m | $18,618.8m | 83.4% | 16.6% |
| **2021** | $29,595m | $21,107.3m | 71.3% | 28.7% |
| **2022** | $29,587m | $21,546.2m | 72.8% | 27.2% |
| **2023** | $24,929m | $18,615.5m | 74.7% | 25.3% |
| 2024 | $25,662m | $21,487.4m | 83.7% | 16.3% |
| 2025 | $22,709m | $19,374.4m | 85.3% | 14.7% |
| **2015–2025** | **$247,409m** | **$199,811.3m** | **80.8%** | **19.2%** |

Two further limits are worth stating plainly. The data carries a **year, not a month**, so it cannot establish that a payment followed any particular event. And it records the **country of registration, not ownership**: a firm registered in the Russian Federation may be foreign-owned, and a Russian-owned firm registered anywhere else does not appear here at all, however Russian it is in substance.

How the 2025 break was established, and how many rows in this file it reaches, is set out in [what broke in 2025](#what-broke-in-2025-and-how-much-of-this-file-it-touches) below.

## What broke in 2025, and how much of this file it touches

Point 3 says the country field became unreliable in 2025.

Three innocent explanations exist, and all three are reasonable. A company can re-register in another country part-way through a year. Two different companies can carry the same name, the more so because the source truncates names at about 34 characters. And a subsidiary abroad trades under its parent's name. None of these is a fault in the data.

So the question was asked again in a different unit: not per supplier name, but per contract. In the full corpus the contract reference behaves as a near-perfect key. Of 827,608 references, not one is attached to two different organizations and not one to two different years; only 0.85% carry two supplier names.

A contract showing several countries under several supplier names means the reference was used for more than one company, and nothing is broken. A contract showing several countries under a single supplier name means one company is recorded as resident in several countries within one contract, and no re-registration, namesake or subsidiary can produce that.

| Year | Contracts | With more than one country | …under a single supplier name | …under several names |
| ---: | ---: | ---: | ---: | ---: |
| 2015 | 51,723 | 480 | 8 | 472 |
| 2016 | 52,361 | 565 | 14 | 551 |
| 2017 | 65,591 | 680 | 17 | 663 |
| 2018 | 65,433 | 850 | 17 | 833 |
| 2019 | 70,326 | 923 | 18 | 905 |
| 2020 | 79,384 | 1,324 | 36 | 1,288 |
| 2021 | 85,072 | 1,268 | 46 | 1,222 |
| 2022 | 94,784 | 801 | 22 | 779 |
| 2023 | 88,847 | 9 | 0 | 9 |
| 2024 | 94,119 | 129 | 11 | 118 |
| **2025** | 79,968 | 5,694 | **5,679** | 15 |

The fourth column is the one that matters. In every year from 2015 to 2024 the genuinely broken contracts number between zero and 46, and almost all multi-country cases are shared references, the innocent kind.

In 2025 it inverts: 5,679 of 5,694. Measured this way the jump is not fourteenfold but roughly 270-fold, from 0.026% of contracts in a typical year to 7.10%.

One case. `FAIRMOUNT WEATHER SYSTEMS LTD` is a British maker of weather observation equipment, present in this data since 2016 and always registered in the United Kingdom. In 2025 it appears under seven countries: Haiti, Mozambique, the Russian Federation, Switzerland, Syria, Ukraine and the United Kingdom. All seven rows carry the same contract reference, `WFP-2025-9c08b620d1e2`.

One more explanation had to be ruled out, and it was ours. This file is assembled from three separate downloads, and a column shift while merging them would produce exactly the same picture: one contract, one supplier, several countries. So the same count was run on the raw downloads, before anything was merged. It matches for all eleven years, to the unit, including 2025 with its 5,694 multi-country contracts of which 5,679 carry a single supplier name. The break arrived from the source — our own handling did not create it.

### How much of it reaches this file

| Year | Rows in this file | On multi-country contracts | Share of rows | Value affected | Value that year |
| ---: | ---: | ---: | ---: | ---: | ---: |
| 2015 | 340 | 1 | 0.3% | $675,000 | $366,741,627 |
| 2016 | 224 | 0 | 0.0% | $0 | $328,631,292 |
| 2017 | 430 | 4 | 0.9% | $134,210 | $300,137,079 |
| 2018 | 584 | 5 | 0.9% | $329,287 | $318,982,218 |
| 2019 | 586 | 4 | 0.7% | $428,000 | $286,014,775 |
| 2020 | 552 | 4 | 0.7% | $235,665 | $263,897,022 |
| 2021 | 521 | 2 | 0.4% | $153,910 | $230,717,675 |
| 2022 | 486 | 2 | 0.4% | $310,123 | $204,365,270 |
| 2023 | 109 | 0 | 0.0% | $0 | $74,760,673 |
| 2024 | 144 | 0 | 0.0% | $0 | $103,079,118 |
| **2025** | 134 | 18 | 13.4% | $9,695,307 | $93,370,339 |

In 2025, 18 of the 134 rows here sit on a contract whose country field cannot be trusted: $9.7 million, or 10.4% of the money this file records for that year. In every other year the share is under 1%, and in four years it is nothing at all.

Five of those eighteen rows name a supplier, and not one of the five looks Russian:

| Supplier | Value | Countries on the same contract |
| --- | ---: | --- |
| `FAIRMOUNT WEATHER SYSTEMS LTD` | $250,000 | Haiti, Mozambique, Russian Federation, Switzerland, Syrian Arab Republic, Ukraine, United Kingdom |
| `WALTONS  NAMIBIA (PTY) LTD.` | $175,000 | Colombia, Russian Federation, Ukraine, Uruguay |
| `FFAC SOCIEDAD ANONIMA` | $75,000 | Afghanistan, Kenya, Russian Federation |
| `LA ROSEE SARL` | $50,000 | Afghanistan, Bangladesh, Denmark, Kenya, Russian Federation, Tanzania |
| `POWERMEDICAL ECUADOR S.A.S.` | $50,000 | Nigeria, Russian Federation, Switzerland |

The other thirteen are withheld names, and they carry almost all of the $9.7 million. There is no way to check those by eye, by construction.

What none of this proves is that any particular row is wrong. A multi-country contract shows that the country field on that contract is unreliable; it does not say which of its rows is the mistake. Some of those eighteen may be genuinely Russian. Settling that would need a source outside this dataset, and we have not done it yet.

## Does the UN restate past years?

Every edition of the Annual Statistical Report reprints the earlier years next to the new one, so the 2015 total is printed not once but in every edition since. If those reprints disagreed, "the UN's own total" would not be one fact, and the 19.2% gap above would depend on which edition you happened to open. So we checked.

We read Figure 1, "Total procurement of goods and services", out of every edition of the report and built a matrix of year against edition. Over 2015–2025 the series barely moves. The 2015 total is the identical $17,575 million in all eleven editions that carry it, and every year from 2020 to 2025 is identical wherever it appears.

Four years differ, and only in two editions. The 2020 and 2021 editions put 2016 at $17,723m, 2017 at $18,628m, 2018 at $18,788m and 2019 at $19,895m: higher than every other edition by $10m, $5m, $3m and $2m, which is 0.056%, 0.027%, 0.016% and 0.010% of those years. These are not figures that settled over time and were later corrected. The 2016 edition published $17,713m for 2016 in the first place, the editions before and after say the same, and only those two say otherwise. The reports do not explain it, and we do not know what caused it.

| Year | 2015 | 2016† | 2017 | 2018 | 2019 | 2020 | 2021 | 2022 | 2023 | 2024 | 2025 |
| ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| 2015 | 17,575 | 17,575 | 17,575 | 17,575 | 17,575 | 17,575 | 17,575 | 17,575 | 17,575 | 17,575 | 17,575 |
| 2016 |  | 17,713 | 17,713 | 17,713 | 17,713 | **`17,723`** | **`17,723`** | 17,713 | 17,713 | 17,713 | 17,713 |
| 2017 |  |  | 18,623 | 18,623 | 18,623 | **`18,628`** | **`18,628`** | 18,623 | 18,623 | 18,623 | 18,623 |
| 2018 |  |  |  | 18,785 | 18,785 | **`18,788`** | **`18,788`** | 18,785 | 18,785 | 18,785 | 18,785 |
| 2019 |  |  |  |  | 19,893 | **`19,895`** | **`19,895`** | 19,893 | 19,893 | 19,893 | 19,893 |
| 2020 |  |  |  |  |  | 22,338 | 22,338 | 22,338 | 22,338 | 22,338 | 22,338 |
| 2021 |  |  |  |  |  |  | 29,595 | 29,595 | 29,595 | 29,595 | 29,595 |
| 2022 |  |  |  |  |  |  |  | 29,587 | 29,587 | 29,587 | 29,587 |
| 2023 |  |  |  |  |  |  |  |  | 24,929 | 24,929 | 24,929 |
| 2024 |  |  |  |  |  |  |  |  |  | 25,662 | 25,662 |
| 2025 |  |  |  |  |  |  |  |  |  |  | 22,709 |

Each row is a year, each column an edition of the report, in millions of US dollars. The eight shaded cells are the ones that disagree with every other edition. Blank means the year is not in that edition's figure: an edition cannot report a year that has not happened yet. † marks the one column read off the page by hand rather than by machine — see the limit below.

The figures used here come from the 2025 edition, and its column matches the year-by-year table for all eleven years, to the million.

One limit worth stating. Ten of the eleven editions were read by machine; the 2016 edition could not be, because the text layer of that page is drawn in a way that shifts the columns, so its two relevant years were read off the page by hand instead. And this checks what Figure 1 prints: a revision made elsewhere in a report, or inside the dashboard itself, would not show up here.

## Both ends of a row are unknown

The `organization` column says which UN organization paid, and almost never anything finer. Three of its 24 values name a Secretariat duty station or a regional commission (`UNOG`, `UNOV`, `ESCAP`, 23 rows between them); the rest are whole organizations. When a row says `WFP`, `UN Secretariat`, `UNICEF` or `UNDP` — 3,570 of the 4,110 rows, 87% of the file — there is no way to tell whether the buyer was a country office, a regional hub or headquarters.

The other end of the chain is missing altogether. There is no project, no programme, no mission and no recipient country here: the seven columns above are all there is. So each row leaves both ends unknown: which part of the organization bought, and who the purchase was for.

That second gap is the one most likely to be misread. `UNICEF` next to `Russian Federation` means UNICEF paid a supplier registered in Russia. It does not mean UNICEF was buying for Russia. Pharmaceuticals, contraceptives and vaccines are the third largest category in this file at $357.3 million, and $320.1 million of that is UNICEF alone, bought from Russia-registered suppliers between 2015 and 2025. Where any of it was delivered is not recorded here, and cannot be: this file has no recipient country. Any claim about where a purchase went has to come from a source outside this dataset.

## What this data cannot say: why

The UN publishes who won a contract. It does not publish who competed for it: there is no bidder list, no count of bids received, no losing offer, and no procurement method — whether a purchase was openly tendered, awarded directly, or drawn from a long-term agreement. None of that is in this file, and none of it is in the dashboard this file comes from.

That absence is why the fall in payments to Russia-registered suppliers is described here and not explained. At least five accounts fit the same numbers equally well, and this dataset cannot separate them:

- Russian suppliers stopped bidding, or bid less often than before.
- They kept bidding and lost, to competitors who grew stronger, cheaper or easier to pay.
- UN organizations chose to buy elsewhere. Such a choice may be written policy or unwritten caution, and in either case it leaves no trace in a record of payments.
- Suppliers re-registered outside Russia. This file is built on the country of registration, so a company that moves its registration to another country simply leaves it, while the business may continue unchanged: same owners, same goods, same buyers.
- What the UN was buying changed. If demand for a category falls, the suppliers of that category fall with it, whatever their nationality and whatever anyone decided about them.

What the fall is not is the system shrinking. Over the same years UN procurement as a whole grew. On the same basis as this file, the dashboard holds $15.9 billion for 2015 and $19.4 billion for 2025, a rise of 22%. On the UN's own totals, which also count purchases below the $30,000 threshold, 2015 to 2025 is a rise of 29%.

Payments to Russia-registered suppliers went the other way, from $366.7 million in 2015 to $93.4 million in 2025, a fall of 75%. Whatever happened, it did not happen to everyone.

### How the UN's own total moved, year by year

**That rise was not a steady climb, and it has been going the other way lately.** From 2015 to 2019 the UN's own total barely moved, from $17.6 billion to $19.9 billion. Nearly all of the increase arrived in two years, 2020 and 2021, which carried it to about $29.6 billion — the highest level in the series, where it stayed through 2022. It has not returned there since: 2023 fell 15.7%, 2024 recovered 2.9%, and 2025 fell 11.5%, leaving $22.7 billion, the lowest figure since 2020. This matters for how the 29% should be read. It is the distance between the two ends of an eleven-year series, not a rate of growth that held throughout: measured to 2021 the same totals rise 68%, and to 2024, 46%. Every year in the series is still above 2015, so the direction of the comparison does not change — only its size does, depending on where you stop. **Why the totals moved this way is not in this data, and is not guessed at here.** The reports give the figures, not the reasons.

| Year | UN's own total | Change on the year | Against 2015 |
| ---: | ---: | ---: | ---: |
| 2015 | $17,575m | +2.0% | — |
| 2016 | $17,713m | +0.8% | +0.8% |
| 2017 | $18,623m | +5.1% | +6.0% |
| 2018 | $18,785m | +0.9% | +6.9% |
| 2019 | $19,893m | +5.9% | +13.2% |
| 2020 | $22,338m | +12.3% | +27.1% |
| **2021** | $29,595m | +32.5% | +68.4% |
| **2022** | $29,587m | -0.0% | +68.3% |
| 2023 | $24,929m | -15.7% | +41.8% |
| 2024 | $25,662m | +2.9% | +46.0% |
| 2025 | $22,709m | -11.5% | +29.2% |
| **Average, 2015–2025** | **$22,491.7m** | — | — |

The 2025 total sits **1.0% above that eleven-year average**. The totals are the UN's own, read from Figure 1 of its Annual Statistical Report; the percentages are worked out from them. Whether those published totals shift between editions is examined under [Does the UN restate past years?](#does-the-un-restate-past-years) below.

## Provenance and integrity

Extracted from the **Major purchase orders and contracts** dashboard, the row-level table on the UN's *procurement by supplier* page of the Annual Statistical Report knowledge centre, downloaded 20 July 2026. That page carries two dashboards, and this matters: the first is an aggregated overview of suppliers and totals, the second is the contract-line table this file comes from, the one showing 994,900 rows. Figures taken from the overview will not match this file row for row.

Three things about how the UN compiles this are worth knowing, and each is stated by the UN itself.

**The figures are contract amounts, not money spent.** In the UN's words, "purchase orders and contracts for services are reported by contract amount and not by expenditures incurred", because many organizations cannot report actual expenditure. A contract cut short, renegotiated or never fully performed still appears here at its full value ([the UN's own explanation of this data](https://www.ungm.org/Shared/KnowledgeCenter/Pages/asr_about)). Where this README says a supplier was paid, read it as the value of what was contracted.

**The supplier country is whatever the submitting organization reported.** The UN defines the field as the supplier country given by that organization, "usually the country where the supplier is registered". Usually, not always. And the organizations themselves are, again in the UN's words, "ultimately responsible for the accuracy and completeness of the data they submit" ([the UN's own explanation of this data](https://www.ungm.org/Shared/KnowledgeCenter/Pages/asr_about)).

**Not every figure started out in dollars.** Organizations may report in US dollars, euros or Swiss francs, and each one decides for itself how to convert from the currency a purchase was actually made in. Euro and franc figures are then converted at average UN Treasury rates ([the UN's own explanation of this data](https://www.ungm.org/Shared/KnowledgeCenter/Pages/asr_about)).

One more thing, if you go checking these numbers against an agency's own published statistics: they may not match, and the UN says so. Its example is UNDP, which counts its National Implementation Modality in its own procurement figures but not in what it submits for this report ([the UN's own explanation of this data](https://www.ungm.org/Shared/KnowledgeCenter/Pages/asr_about)).

The file is sealed with SHA-256 so that any copy can be checked against the original:

```
file   russia-suppliers_2015-2025.csv
sha256 0620474a1c247db87b4d1575acaa9a7a6b484505f238afabf082569efa5cec9f
```

`data/manifest.csv` carries the same fingerprint in machine-readable form. The seal of the manifest itself — one fingerprint for the whole dataset — is `859ec378a750d85826adab5b35e403033bb1b0d947dbab0c4379c35e04b17cd7`. To check a copy on macOS or Linux:

```bash
shasum -a 256 data/russia-suppliers_2015-2025.csv
```

The repository sets `*.csv -text` so that git never rewrites line endings in the published file. Without that, a clone on another machine could produce different bytes and a different fingerprint from a file nobody edited.

## Open it in your browser, without downloading anything

[**Explore this file in Datasette Lite**](https://lite.datasette.io/?csv=https://nakoua.github.io/un-russia-procurement/data/russia-suppliers_2015-2025.csv)

Datasette Lite opens the same CSV as a searchable table: sort by value, filter by year, organization or category, or write SQL against it. There is nothing to install and nothing to download. It fetches the file from this repository and does all the work inside your own browser, so no copy of the data is sent to us or to anyone else.

One honest caveat. It is a third-party service we do not run, and it needs a few seconds to start, because it loads a small Python environment into the browser before it can read anything.

## What is deliberately not published here

Two pieces of the underlying analysis are held back, by our own verification standard. A timeline of sanctions against suppliers is withheld because only one of its nine dates has been checked against the primary act. Figures for suppliers whose names were withheld in 2025 are held back because the source's country field broke that year, and those rows carry no name against which the country could be checked. One explanation has since been ruled out: the break is not an artefact of our own processing, because the same anomaly is present in the raw download, before any file of ours was assembled. What is still unresolved is which of those rows are genuinely Russian. Both will be published if and when they are verified.

## How this was made

The analysis behind this dataset was done in Claude Code with a set of Agentic Skills written for investigative work: small deterministic tools that seal a corpus so a re-run can be proven to start from identical bytes, keep a ledger of candidate findings with a reason recorded for every one that is killed, assemble the underlying records into case files, and recompute every derived figure from the tables it came from.

Nine of them are published under an MIT licence as part of a submission to [the Agentic Investigation Challenge](https://generative-ai-newsroom.com/announcing-the-winners-of-the-agentic-ai-investigative-challenge-76f119cc35ca).

[That set](https://github.com/comp-journalism/aaijc-workflows-release/tree/main/submissions/registry-nako) was built for a different body of material, the US federal lobbying filings. It is still being developed, and the UN procurement corpus behind this repository is one of the datasets it is being refined and tested against.

Four of the nine were used directly on the work published here: `pin-corpus` to seal the source files, `track-leads` for the ledger of candidate findings, `build-casefiles` to gather the primary records behind each one, and `verify-derived-numbers` to recheck the arithmetic in this README against the tables underneath it.

## Licensing

This repository is mixed, so different parts carry different terms. [LICENSE](LICENSE) covers the code; everything with other terms is set out in [NOTICE.md](NOTICE.md).

| What | Licence |
| --- | --- |
| Source code (`index.html`) | [MIT](LICENSE) |
| Data (`data/`) | [CC BY 4.0](LICENSE-data.md) |
| Written text (this README, the prose on the page) | [CC BY 4.0](NOTICE.md) |
| Fonts (`fonts/`) | IBM Plex, [SIL OFL 1.1](NOTICE.md) |

All of it is open, and that is the point of publishing it this way. Take the data, recompute these figures, and publish what you find; take the text, translate it, republish it. The only condition is that you credit the source — the exact credit lines are in [LICENSE-data.md](LICENSE-data.md) for the data and [NOTICE.md](NOTICE.md) for the text.

One thing that is asked rather than required: the numbers here are hedged for reasons set out above, and they stop being true without those caveats. If you take the figures, take the caveats with them.

## Corrections

If a number here does not match what you find at source or if there is anything else worth pointing out, we want to know.

Two ways to tell us, whichever is easier: open an issue on this repository, or just write to registry.nako[at]gmail.com.

Point at the figure and where you looked. That is enough, you do not need to explain the whole thing.

We read everything and reply within 14 days: either the correction is made, or we come back to you with a question if we need more detail to check it.
