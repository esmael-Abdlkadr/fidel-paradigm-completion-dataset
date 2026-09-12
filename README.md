# Fidel Paradigm Completion Dataset

An original synthetic benchmark of 600 invented Ethio-Semitic dialects with hidden templatic (root-and-pattern) morphology, written in the Ethiopic fidel syllabary. Each dialect supplies the citation form of 40 lexemes and 80 attested inflected forms over 19 grammatical cells; the task is to complete 84 missing paradigm cells per dialect. Dialects differ in their vowel templates, affixes and sound rules (glide fusion, palatalisation, nasal assimilation, natural-class prefix alternation, final-vowel raising), all modelled on Amharic verb morphology but entirely invented.

- Licence: CC BY 4.0. Attribution: Esmael Abdlkadr, *Fidel Paradigm Completion Dataset* (2026).
- Contents of this repository: `DATASET_DESCRIPTION.md` (the full dataset card, identical to the copy inside the release archive) and `LICENSE`.
- The release archive itself is distributed on the challenge platform, not here.

## Reproducibility and answer safety

Every random draw, identifier and grammar in the release is derived by HMAC-SHA256 from a 256-bit secret held by the creator and never published. The generator therefore cannot be run by anyone else to reproduce the released dialects or their answer keys, and no seed, index or identifier scheme in the release is enumerable back to them. Running an equivalent generator with a different secret yields a statistically equivalent dataset, not this one.

The construction procedure is documented in full in `DATASET_DESCRIPTION.md`: the template and affix menus, the five sound rules and their dialect-specific parameters, the root sampling, the irregularity rate, and the split. Reviewers who need the construction code to audit those claims can request it.

The dataset contains no natural-language corpus, no real lexicon, no user data and no third-party records. Every string was produced by the generator.
