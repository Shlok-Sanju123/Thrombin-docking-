# Thrombin Virtual Screening — Computational Docking Pipeline

A worked, reproducible example of virtual screening / molecular docking against human thrombin, built as a learning project. Documents a validated CB-Dock2 docking pipeline with a full worked example — intended as a reference for other students getting started with computational drug discovery, not as a claim of novel drug discovery findings. **Screening complete: 20 compounds tested.**

## Disclaimer

This is a small-scale (~20 compound) educational screen, not a rigorous drug discovery study. Results are computational predictions only, have not been validated experimentally, and should not be interpreted as evidence that any compound listed is or isn't a viable thrombin inhibitor. See "Limitations" below.

## Target

**Protein**: Human thrombin (Factor IIa)
**Structure used**: [PDB 1KTS](https://www.rcsb.org/structure/1KTS) — thrombin in complex with dabigatran (ligand code C24, referenced in the literature as compound 24 / BIBR 953) at the active site, and a hirudin fragment (from *Hirudo medicinalis*) bound at a separate exosite.
**Why thrombin**: central to the blood clotting cascade; relevant across multiple related conditions (stroke, heart attack, pulmonary embolism, deep vein thrombosis) rather than a single disease. Extensively characterized in the literature, which allows methodology validation against known results (see below).

## Method

1. Protein structure downloaded from RCSB PDB (1KTS), water molecules and heteroatoms (including the co-crystallized dabigatran and hirudin fragment) stripped prior to docking to allow independent, unbiased pocket detection.
2. Candidate compounds downloaded from PubChem (3D SDF format).
3. Docking performed using [CB-Dock2](https://cadd.labshare.cn/cb-dock2) (blind docking; pockets auto-detected, AutoDock Vina scoring).


## Validation (Redocking Test)

Since 1KTS already contains dabigatran in its known, real position, dabigatran was removed and re-docked from scratch as a validation check — testing whether the pipeline could independently rediscover the known correct binding pose, not just confirm information already present in the structure.

**Result: validated.**
- Score: **-9.4 kcal/mol** (strong binder range)
- Top pocket's contact residues included **Asp189, Trp215, Gly216** — matching literature-reported active site residues
- CB-Dock2's structural template match returned **"1kts"** — i.e., it independently identified the same pocket as the original crystal structure

This gives confidence that the pipeline correctly identifies the real, biologically relevant active site rather than an arbitrary surface pocket.

## Full Results — All 20 Compounds (Ranked by Score, Strongest to Weakest)

Screening complete. Scores below use the pocket judged most representative per compound (see `NOTES.md` for reasoning where the top-ranked and best-scoring pockets disagreed).

| Rank | Compound | Score (kcal/mol) | Category | Notable? |
|---|---|---|---|---|
| 1 | **Dabigatran** | **-9.4** | Anticoagulant — direct thrombin inhibitor | Positive control — redocking validation passed (matches literature active site + original crystal pocket) |
| 2 | Diazepam | -8.6 | Anxiety/sedation (benzodiazepine) | Unrelated mechanism — cross-reactivity flag |
| 3 | Apixaban | -8.5 | Anticoagulant — Factor Xa inhibitor | Different clotting target than thrombin — cross-reactivity flag |
| 4 | Atorvastatin | -8.4 | Cholesterol (statin) | Unrelated mechanism — cross-reactivity flag |
| 4 | Loratadine | -8.4 | Allergy (antihistamine) | Unrelated mechanism |
| 6 | Sertraline | -8.1 | Depression/anxiety (SSRI) | Unrelated mechanism |
| 6 | Warfarin | -8.1 | Anticoagulant — vitamin K antagonist | Different mechanism than thrombin direct inhibition — cross-reactivity flag |
| 8 | Cetirizine | -7.4 | Allergy (antihistamine) | Same class as loratadine, weaker score |
| 9 | Losartan | -7.3 | Blood pressure | Unrelated mechanism |
| 10 | Simvastatin | -7.0* | Cholesterol (statin) | Same class as atorvastatin, notably weaker score |
| 11 | Furosemide | -6.8 | Diuretic | Unrelated mechanism |
| 12 | Clopidogrel | -6.4 | Antiplatelet | Related to clotting broadly, not thrombin specifically |
| 13 | Rivaroxaban | -6.2* | Anticoagulant — Factor Xa inhibitor | Same class as apixaban, notably weaker score |
| 14 | Amlodipine | -5.9 | Blood pressure | Unrelated mechanism |
| 15 | Acetaminophen | -5.7 | Pain/fever | Negative-ish control — moderate/weak as expected |
| 16 | Metformin | -5.5 | Diabetes | Weakest overall pocket spread — small polar molecule, didn't fit the usual permissive pocket |
| 17 | Naproxen | -5.2* | NSAID | Unrelated mechanism |
| 18 | Aspirin | -5.0 | Pain/anti-inflammatory |  Negative control — weak *and* best-scoring pocket; not the real active site |
| 19 | Ibuprofen | -4.7* | NSAID | Unrelated mechanism |
| 20 | Metoprolol | -3.6* | Blood pressure | Weakest top-pocket score, though an alternate pocket scored comparably to mid-table results |

*Compounds marked with an asterisk had a top-ranked-vs-best-scoring pocket discrepancy that wasn't fully resolved with a contact-residue check  — treat their exact rank position with some caution.

## Key Takeaways

1. **Pipeline validated**: dabigatran's redocking result (score, contact residues, and template match all independently confirming the real crystal pocket) gives confidence the pipeline correctly identifies thrombin's real active site.
2. **Negative controls behaved as expected**: aspirin and acetaminophen, with no known thrombin-binding mechanism, scored weakly — aspirin additionally failed to even engage the correct pocket at its best-scoring position.
3. **Several unrelated compounds scored strong-to-moderate** (diazepam, apixaban, atorvastatin, loratadine, sertraline, warfarin) in the same large, recurring pocket — most plausibly explained by that pocket's size/permissiveness rather than genuine thrombin specificity for each one (see Limitations).
4. **Same drug class ≠ similar score**: three independent same-mechanism pairs (antihistamines, Factor Xa inhibitors, statins) showed meaningfully different scores against thrombin, reinforcing that a single blind docking run captures substantial pose-specific noise.
5. **Molecule size/character may matter**: metformin, a small polar outlier, was the only compound that didn't prefer the large permissive pocket — suggesting that pocket's permissiveness has limits.

## Limitations

- Small library size (~20 FDA-approved compounds) — not a comprehensive screen.
- Single docking tool / single scoring function (AutoDock Vina via CB-Dock2) — no consensus scoring across multiple algorithms.
- Docking score is a computational prediction of binding potential, not proof of biological activity — no wet-lab or cell-based validation has been performed.
- Any strong-scoring "hit" here would require experimental follow-up before being considered a genuine finding.


## Repository Contents

- `results final.csv` — full compound-by-compound docking results
- `README.md` — this file

