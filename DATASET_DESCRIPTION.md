# Fidel Paradigm Completion Dataset

## Overview

This is an original, fully synthetic dataset of 600 invented Ethio-Semitic dialects with hidden templatic (root-and-pattern) morphology, written in the Ethiopic fidel syllabary. Each dialect has its own hidden consonantal roots, vowel templates, affixes and sound rules, all drawn from menus modelled on Amharic verb morphology. The dataset contains no natural-language corpus, no real lexicon, no user data and no third-party records; every string was produced by the generator described below.

The raw upload contains 600 dialects in 30 grouping families of 20 dialects each. Every dialect has 40 lexemes, 19 grammatical cells, 80 attested inflected forms and 84 query cells. The labels are the forms of the query cells, the hidden root of every lexeme, and the set of sound rules active in the dialect.

## Release At A Glance

- Raw files: 11
- Dialects (cases): 600
- Families (grouping keys): 30, with 20 dialects each
- Lexemes per dialect: 40, each with a citation form
- Grammatical cells: 19
- Attested inflected forms per dialect: 80
- Query cells per dialect: 84, of which 24 belong to 8 lexemes with only a citation form
- Script: Ethiopic Unicode block, 25 consonant series, 7 vowel orders
- Prepared split: 24 families / 480 training dialects, 6 families / 120 test dialects
- Data origin: creator-generated synthetic data, version 1

## Raw File Structure

The uploaded ZIP is flat and contains exactly these eleven files at its root:

- `languages.csv`: one record per dialect: `case_id` and its family key.
- `lemmas.csv`: one record per lexeme: `case_id`, `lexeme_id`, `lemma` (the citation form, cell `PFV.3SG.M`, in fidel).
- `attested_forms.csv`: one record per attested inflected form: `case_id`, `lexeme_id`, `cell`, `form`. Eighty records per dialect.
- `query_cells.csv`: one record per cell to be completed: `case_id`, `lexeme_id`, `cell`. Eighty-four records per dialect. Attested forms and query cells are separate tables so that no column is ever empty.
- `labels.csv`: one creator-side record per dialect: `case_id`, `answers_json` (JSON object from query key `lexeme_id|cell` to the true surface form), `roots_json` (JSON object from `lexeme_id` to the hidden root, written as its three consonants in the vowelless sixth order, for example ስብር for s-b-r) and `rules_json` (JSON array of the active rule names); used by `prepare.py` and never copied into public prepared data.
- `source_metadata.json`: provenance, scale, cell inventory, rule-name inventory, seed policy and licence metadata.
- `LICENSE`: CC BY 4.0 notice and licence URL.
- `ATTRIBUTION.txt`: attribution text.
- `DATASET_CARD.md`: short scope and safety summary.
- `DATASET_DESCRIPTION.md`: this document.
- `PACKAGE_MANIFEST.sha256`: SHA-256 checksum of every other raw file.

## Columns

### languages.csv

- `case_id` (string): opaque dialect identifier, a keyed hash, for example `lang_3f9c1a7b2e`.
- `family_id` (string): opaque grouping key used only to build family-held-out splits.

### lemmas.csv

- `case_id` (string): joins to `languages.csv`.
- `lexeme_id` (string): opaque lexeme identifier, unique within a dialect, for example `lex_9a1c4e`.
- `lemma` (string): the citation form in fidel, for example ሰበረ.

### attested_forms.csv

- `case_id` (string) and `lexeme_id` (string): as above.
- `cell` (string): one of the 19 cell tags listed below.
- `form` (string): the attested surface form in fidel.

### query_cells.csv

- `case_id` (string) and `lexeme_id` (string): as above.
- `cell` (string): the cell whose form is to be predicted. A query is never an attested pair and never the citation cell.

### labels.csv

- `case_id` (string): joins to the other tables.
- `answers_json` (JSON object string): query key `lexeme_id|cell` to the true form of every query record of that dialect.
- `roots_json` (JSON object string): `lexeme_id` to the hidden three-consonant root of every lexeme, as three vowelless fidel glyphs.
- `rules_json` (JSON array string): the rule names active in the dialect, from `glide_fusion`, `palatalisation`, `nasal_assimilation`, `prefix_alternation`, `final_raising`.

Cell tags: `PFV.3SG.M` (the citation cell), `PFV.3SG.F`, `PFV.1SG`, `PFV.3PL`, `IPFV.3SG.M`, `IPFV.1SG`, `IPFV.1PL`, `IPFV.2SG.F`, `IPFV.3PL`, `JUS.3SG.M`, `INFIN`, `GER.3SG.M`, `AGN`, `INS`, `NEG.PFV.3SG.M`, `IMP.2SG.M`, `VN`, `PASS.PFV.3SG.M`, `CAUS.PFV.3SG.M`.

Example records, one from each table:

```text
attested_forms.csv:  lang_3f9c1a7b2e,lex_9a1c4e,IPFV.3SG.M,ይሰብር
query_cells.csv:     lang_3f9c1a7b2e,lex_9a1c4e,AGN
```

## How The Data Is Generated

Each dialect draws a grammar: for every cell a vowel template over the three radicals from a small menu (the perfective-family cells share one template, the imperfective cells share another), an optional prefix and suffix from per-cell menus, and a set of two to four sound rules from a pool of five. The rules are fusion of a glide radical (w or y) with an adjacent vowel into a rounded or front vowel, palatalisation of a final radical before a front-vowel suffix, assimilation of a prefix nasal to a labial first radical, a prefix-vowel change before a natural class of first radicals (sonorants, sibilants, dorsals, labials or coronals), and raising of a stem-final low vowel when no suffix follows. Each rule has dialect-specific parameters. Forty roots are then sampled from twenty consonant series, with a third of them forced to contain a glide radical in addition to the glides that occur by chance, and every cell of every lexeme is inflected. Six percent of non-citation lexeme-cells are made irregular by drawing a different template. Eighty attested forms and 60 query cells are sampled from the lexemes that keep their citation form, and three further query cells are drawn for each of eight lexemes whose other forms are withheld entirely.

All randomness, every `case_id`, `family_id` and `lexeme_id`, and every grammar is derived by HMAC-SHA256 from a 256-bit secret held by the creator. The public generator code requires that secret and refuses to run without it, and the secret appears in no released file, in the source repository or in its history. Dialects are written in hashed-id order, so file order carries no generator index.

## Prepared Outputs

`prepare.py` writes a flat public directory plus a private answer directory. Every prepared file is keyed by `case_id` and has one row per dialect.

- public `train.csv` (480 rows): `case_id`, `family_id`, `lemmas_json` (object of `lexeme_id` to citation form), `attested_json` (array of `[lexeme_id, cell, form]`), `queries_json` (array of `[lexeme_id, cell]`).
- public `test.csv` (120 rows): the same columns for the held-out dialects.
- public `train_labels.csv` (480 rows): `case_id` and `prediction_json`, a JSON object with `forms` (the true forms of the training queries), `roots` (the hidden root of every lexeme) and `rules` (the active rule names), in the submission format.
- public `sample_submission.csv` (120 rows): `case_id` and `prediction_json`, with every query answered by its lexeme's citation form, every root read off the citation form's consonants, and no rules claimed; valid and weak.
- private `answers.csv` (120 rows): `case_id` and `prediction_json`, the true roots, rules and forms of the test dialects, a `fused` list naming the lexemes whose citation form hides a radical, and a reserved `__case_ids__` field listing the test dialects. It has the same columns as `sample_submission.csv`, and the grader ignores reserved fields, so the answer key is itself a perfect submission.

The public directory also holds `LICENSE`; no other raw document is copied into it, so nothing a solver receives names the dataset or its author. The 19 cell tags are fixed and stated here rather than repeated per row.

## Characteristics

- Each dialect has its own grammar and roots, so nothing memorised from one dialect transfers as a lookup; what transfers is the space of templates, affixes and rules.
- About half of the queries involve a lexeme with a glide radical, and 21% of all lexemes have a citation form in which the glide has fused into a vowel, so reading the root off the citation consonants is wrong for them and the radical must be reconstructed from the fused vowel.
- Not every active rule leaves a trace in a dialect's released forms: glide fusion and prefix alternation always do, final raising in 72% of dialects, palatalisation in 51%, nasal assimilation in 18%. A rule with no trace cannot be identified from the evidence.
- Rule effects are conditioned on radical identity or natural class, so a query can require evidence from other lexemes of the same class.
- The family keys support leak-free group-held-out splits: no family occurs in both prepared splits.

## Known Limitations

- **Fully synthetic; no real-language validity.** The templates, affixes and rules are modelled on Amharic verb morphology but every dialect is invented. Methods that work here do not constitute results about any real Ethio-Semitic language.
- **Menus are finite.** Every dialect draws from the same menus of templates, affixes and rules, so the hypothesis space is learnable from the training dialects. The held-out split tests induction of unseen dialects, not generalisation to unseen kinds of morphology.
- **Uniform dialect shape.** Every dialect has 40 lexemes, 19 cells, 80 attested forms and 84 queries. Solutions are never tested on larger or ragged lexica.
- **Irregular cells and untraceable rules are unpredictable by construction.** Six percent of lexeme-cells use a different template than their dialect, and an active rule that leaves no trace in a dialect's released forms cannot be identified from them. Both put a ceiling below a perfect score, quantified in the challenge description.
- **Gemination is unwritten.** As in Ethiopic orthography, consonant length is not represented, so the surface forms carry less information than a phonemic transcription would.
- **Reproducibility is restricted by design.** The generator is public, but the released data can be regenerated only with the withheld secret. Anyone auditing the generator can run it with their own secret to obtain a statistically equivalent dataset, not this one.

## Provenance And License

All records are generated by an original deterministic synthetic generator written for this dataset. The package is released under Creative Commons Attribution 4.0 International (CC BY 4.0). Attribution: Esmael Abdlkadr, *Fidel Paradigm Completion Dataset* (2026).
