# OpenADMET CYP3A4 Blind Structure Prediction — Methods Report
 
## Abstract
 
We approached the CYP3A4 blind structure track as an iterative, evidence-driven
selection problem. An ensemble of 
independent pose generators — template-conditioned protein–ligand cofolding,
cross-receptor docking and cofolding, physics-based docking, induced-fit
refinement, and geometry-driven pocket placement — was used to build a candidate
pool per ligand. All candidates were normalized into a common heme reference
frame, validated through a staged physical and stereochemical gauntlet, and
selected using a conservative versioned loop in which each package
was evaluated against measured performance of different approaches and tooling.
Our best submission combined a largely conserved set of co-folded complexes with
a small number of ligand substitutions chosen for catalytic-register
geometry, bounded displacement, and multi-method corroboration.
 
## Method
 
### Pose generation
 
Multiple independent generation lanes were run per ligand:
 
- **Cofolding**: a diffusion-based protein–ligand cofolding model
  (Boltz-2), including runs conditioned on experimental holo structures
  of CYP3A4 and runs cross-referenced against alternate crystal receptors,
  with heme and Fe-proximity pocket constraints.
- **Classical and learned docking**: Vina, GNINA, and DiffDock variants,
  including flexible-sidechain docking against a panel of CYP3A4 crystal
  conformations, to sample pocket-shape diversity that a single receptor
  structure cannot represent.
- **Induced-fit refinement**: restrained local pocket relaxation around
  seeded poses, used both as a generator and as an adjudication lane.
- **Geometry-driven placement**: graph-geodesic and spectral placement
  methods that map pocket lining onto ligand anchor points.
 
### Normalization and reference frame
 
All complexes were expressed relative to a shared reference. Alignment used a
heme-based frame (Kabsch on the Fe atom plus the four pyrrole nitrogens,
followed by a sequence-corresponded pocket Cα refinement) rather than raw file
order, because porphyrin pseudo-symmetry produces large false transforms.
Ligand atom order was canonicalized by graph matching against the deposited
connectivity, with a signed stereochemical check so that incorrect mappings are
rejected.
 
 
### Selection strategy
 
Selection was treated as a versioned experiment:
 
- Each measured attempt changed a bounded number of complexes relative to the best
  selections.
- Classes of change that correlated with losses were retired rather than
  re-weighted.
 
## What was tried
 
| Approach | Outcome |
|---|---|
| Broad template-conditioned cofolding swaps | Positive in small numbers; net-negative when scaled |
| Testing predicted protein backbones with correct-looking ligands | Negative vs. keeping the reference protein and moving only the ligand |
| Affinity-score maximization as primary selector | Negative (worst measured package) |
| Learned pose-quality scoring for filtering | Useful for plausibility ranking |
| Near-incumbent, Fe-proximity-improving refinements | Positive — largest single measured gain |
| Multi-family corroboration (independent generators agreeing on a basin) | Positive as supporting evidence, weak as a standalone gate |
| Swaps on the weakest Fe-contact incumbents | Positive |
| Swaps on already-ligated or strong incumbents | Neutral to negative |
 
## Findings
 
- **Score scaled inversely with novelty**: packages with ~9–11 surgical swaps
  outperformed all larger packages, even when every individual candidate passed
  all physical gates.
- **Protein frame matters more than ligand pose**: keeping the deposited
  reference protein and changing only the ligand consistently beat predicted
  backbone conformations, even when the predicted complexes looked internally
  better.
- **Fe-pocket occupancy is the most reliable single geometric prior** for this
  system: candidates that brought the ligand into the catalytic zone (Fe within
  ~2–3.5 Å of a polar/heavy contact) while staying near the incumbent pose were
  the only swap class that measured positive twice.
- **Internal scores (affinity, learned pose quality) do not separate winning
  from losing swaps.** 
 
## Conclusion
 
The best result came from treating the challenge as sequential experimental
design: generate broadly, validate strictly, change incrementally. 
The measurable gains came from a handful of bounded, chemistry-motivated
ligand edits on weak incumbents, each corroborated by at least two independent
methods.
 
## Tools used (non-exhaustive)
 
Boltz-2 (cofolding, template conditioning), Chai-1 (corroboration/hypotheses), AutoDock Vina and GNINA variants,
DiffDock, RDKit, OpenMM (restrained relaxation), PoseBusters (strict validation), NumPy/SciPy (alignment,
graph, and spectral operations), plus custom Python for frame normalization and clash/stereochemistry checking.
 
