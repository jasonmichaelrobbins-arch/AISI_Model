# Contributing

Corrections and improvements are welcome. The model is meant to stand for a long time without regular updates, so the bar for a change is that it makes the documents more accurate, more consistent or clearer, not that it tracks the news.

## How to contribute

- Open an issue for a factual error, a contradiction between documents, or an unclear passage. Name the document, the section and, where it matters, the primary source you checked against.
- Open a pull request for a proposed change. Keep one subject per pull request so it can be reviewed and merged on its own.
- For a change to the control set (an ID, a priority, a name or a layer assignment), explain the reasoning in the pull request; the IDs are stable identifiers that other people's work refers to, and priorities move only with a reason recorded in `CHANGELOG.md`.

## What a change has to respect

- Accuracy comes first. Claims about protocols, catalogs and regulations are checked against the primary source before they go in, and a version anchor is stated where an identifier depends on one (MITRE ATLAS technique and case study IDs, for example).
- The documents carry no what-changed sections, no version comparisons and no dated commentary; that history lives in `CHANGELOG.md`.
- Body text carries no bold or italic emphasis. Headings and table header rows are the only weight changes. Run-in labels are written as headings or colon-led sentences.
- Every acronym is expanded on first use in each document; mTLS is written after its first spelled-out use; "layer N" is lowercase in running prose and capitalized only in labels and headings.
- Control language describes outcomes sized for any organization, not products or mandates.
- The seven documents, the figures, `controls/`, `schemas/` and `markdown/` are generated from the same sources and stay in step: a change to a control is made once, in the control data, and reflected everywhere.

## Licensing of contributions

By submitting a contribution you agree that it is licensed under the Apache License, Version 2.0, as stated in section 5 of the license. Adaptations and forks must not imply the author's endorsement; see `NOTICE`.
