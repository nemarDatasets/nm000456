# sourcedata

Original release files of the source dataset, as far as they can be redistributed.

## Release files stored under their release names (added 2026-10-11)

69 release files of Pennsieve dataset 696 version 2 (HUMAN-AI) (https://discover.pennsieve.io/datasets/696) are listed under `sourcedata/pennsieve/files/raw_data/edf/` with their release paths.
They take no extra storage: each is a git-annex pointer to the same content as the BIDS file named in
`sourcedata/SOURCEDATA_POINTERS.tsv`.

- 11 are `identical`: byte for byte the released file.
- 58 are `scrubbed`: the privacy-scrubbed copy used in the BIDS part, not the released bytes. Scrubbed EDFs differ from the release only in the header start date (day of month set to 01); the data records are identical (`sourcedata/edf_provenance.tsv`).

The unscrubbed originals are not redistributed here; they remain available from the public source.
