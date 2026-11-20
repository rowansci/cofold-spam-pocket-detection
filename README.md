# Pocket Detection via Spamming Cofolding

![Rural Sitting Room](./rural_sitting_room.jpg)

Data for the blog post [*Pocket Detection via Spamming Cofolding*](https://rowansci.com/blog/pocket-detection-via-spamming-cofolding): 210 PocketWatch proteins (70 development, 140 validation) containing 255 annotated sites. Each protein has 16 Boltz-2 probe cofolds, three ESMFold2 structures, zPocket residue scores and P2Rank outputs.

**Full dataset:** `pocket-cofold-data.zip`, attached to the [latest release](https://github.com/rowansci/cofold-spam-pocket-detection/releases/latest) (about 998 MB; ~3 GB unpacked).

## General info

- `data/targets.csv`: protein IDs, selection groups, lengths and site counts. Validation groups are 40 diversity-selected proteins (100–800 residues by construction) and 100 randomly sampled within length groups.
- `data/probes.csv`: the 16 probe molecules and SMILES.
- `data/settings.json`: model versions and inference, processing and scoring settings.
- `data/results.json`: recovery, top-1 and overlap results; rates are fractions from 0 to 1. `hybrid` means P2Rank + zPocket.

## Per protein contents: `data/<protein_id>/` (inside the zip)

| Path | Contents |
|---|---|
| `inputs/` | Sequence FASTA and input MSA |
| `cofold/raw/` | 16 original Boltz-2 protein–probe structures |
| `cofold/aligned/` | The same structures aligned to the shared reference |
| `cofold/probes.json` | Original confidence outputs, ranking scores, alignment transforms and QC flags for all 16 probes |
| `zpocket/` | Three ESMFold2 structures and one residue-score NPZ; **sample 0 is the shared reference** |
| `p2rank/outputs/` | Predicted-pocket and residue CSVs |
| `labels.json` | Annotated sites and evaluation masks |
| `pockets.json` | Final ranked pocket centers and evaluation results for all three methods |

Start with `pockets.json` for predictions, or load `cofold/aligned/` with zPocket sample 0 to view the overlays. All structures are plain `.cif` files. Coordinates are in angstroms. NPZ residue scores follow sequence order.

## Probe protonation

The probe SMILES in `data/probes.csv` were all written in neutral form, so every probe carries a formal charge of zero. This is wrong for several of them at physiological pH: pyrophosphate, ATP, citrate, etc. This was unintentional, and the cofolds were not re-run.

However, I suspect the impact is not serious. Boltz-2 strips hydrogens and reads ligands as a heavy-atom graph, so neutral and deprotonated forms differ only in the per-atom formal-charge feature, and anecdotal reports of protonation insensitivity exist (e.g. see [this thread](https://github.com/jwohlwend/boltz/issues/499)). The point is to rerun with correct protonation if desired!

## Models

The model was **Boltz-2**, executed using the Boltz implementation in the [DeCAF repository](https://github.com/genesistherapeutics/decaf). zPocket used ESMFold2 and its released pairformer2 head; P2Rank 2.5.1 used the `alphafold` configuration on the same ESMFold2 sample 0 fold. All included proteins have complete raw outputs.

Sources: [PocketWatch](https://huggingface.co/datasets/genesisml/PocketWatch), [zPocket](https://huggingface.co/genesisml/zPocket). Data only.
