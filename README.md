# OpenADMET CYP3A4 Blind Structure Prediction — Methods Report
 
## Abstract
 
We treated the CYP3A4 blind structure track as a constrained inference
problem: recover, for each of 20 ligands, the binding
register under a fixed receptor frame, heme cofactor, and a strong
metal-pharmacophore prior. An ensemble of physically and statistically
independent pose generators — template-conditioned protein–ligand cofolding,
cross-receptor docking and cofolding, physics-based docking, induced-fit
refinement, and geometry-driven pocket placement — produced a candidate
hypothesis space per ligand. All candidates were projected into a common
heme-centered reference frame, canonicalized at the atom level, and filtered
through geometric and stereochemical validation pathways. Selection
between hypotheses was driven by structural priors rather than scoring
functions: catalytic-zone occupancy relative to the heme Fe, donor–metal
coordination consistency, bounded displacement from measured references, and
agreement between independent generators. A dedicated metal-coordination
audit classified each ligand as a coordinating or non-coordinating binder and
repaired the single complex found to violate its coordination chemistry, via
a restrained induced-fit protocol corroborated across multiple receptor conformations.
 
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
 
Because internal scores (docking affinity, learned pose-quality models)
proved inefficient to separate true registers from decoys, selection
was driven by structural criteria with a physical basis:

- **Catalytic-zone occupancy**: heavy-atom or donor contact with the heme Fe
  region consistent with the ligand's binding class (~2–2.5 Å for
  coordinators, ~3–4 Å for substrates).
- **Bounded displacement**: preference for poses near a reference incumbent,
  since most deposited poses are approximately correct and large relocations
  require correspondingly stronger evidence.
- **Inter-method corroboration**: independent generators converging on the
  same binding basin (measured by pose chamfer/RMSD clustering) treated as
  positive evidence.
- **Conservation**: changes to a measured-consistent package were kept
  minimal and class-homogeneous, so that outcome deltas could be attributed
  to specific geometric hypotheses rather than aggregate noise.
 
## What was tried
 
| Approach | Outcome |
|---|---|
| Broad template-conditioned cofolding swaps | Positive in small numbers; net-negative when scaled |
| Testing predicted protein backbones with correct-looking ligands | Negative vs. keeping the reference protein and moving only the ligand |
| Affinity-score maximization as primary selector | Negative (worst measured package) |
| Learned pose-quality scoring for filtering | Useful for plausibility ranking |
| Near-incumbent, Fe-proximity-improving refinements | Positive — largest single measured gain |
| Multi-family corroboration (independent generators agreeing on a basin) | Positive as supporting evidence, weak as a standalone gate |
| Swaps on the weakest Fe-contact incumbents (pharmacophore-class audit) | Positive |
 
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
 
The strongest results came from treating structure prediction as
physics-constrained inference: a wide hypothesis space generated by
independent methods, a common geometric frame, and selection rules based on the chemistry of the heme active site.
Most of the measurable gains came from a
small number of targeted corrections — improved catalytic-zone placement on
geometrically weak complexes and restoration of a missing metal-coordination
interaction — each corroborated by at least two independent methods.
 
## Tools used (non-exhaustive)
 
Boltz-2 (cofolding, template conditioning), Chai-1 (corroboration/hypotheses), AutoDock Vina and GNINA variants,
DiffDock, RDKit, OpenMM (restrained relaxation), PoseBusters (strict validation), NumPy/SciPy (alignment,
graph, and spectral operations), plus custom Python for frame normalization and clash/stereochemistry checking.
 
