# PT Smart Retail — Contributions from IST / IST-ID / ISR

**Signal and Image Processing Group (SIPG), Institute for Systems and Robotics (ISR),
Instituto Superior Técnico, Universidade de Lisboa**

Open-source software, datasets and research code produced by **IST**, **IST-ID** and **ISR**
within the **PT Smart Retail** agenda.

---

## The project

**PT Smart Retail** is an agenda for business innovation funded by the Portuguese Recovery and Resilience
Plan (PRR) and led by **[Sensei](https://www.sensei.tech)**. It brings together industrial, technological,
scientific and end-user partners to design, demonstrate and industrialize a new generation of autonomous
retail formats — autonomous stores, modular pods and smart cabinets — together with the technologies that
support them. Our part is the computer vision and machine learning stack that lets a store understand what
happens inside it.

## Our contribution

An autonomous store is an unusually dense sensing infrastructure: hundreds to thousands of cameras,
crowded aisles, severe occlusions and a catalogue that changes every week, under strict privacy and energy
budgets. We work on the four layers that make this tractable — fast **calibration** of large camera
networks, **edge deployment** of vision algorithms as containerized services, **perception** robust to the
occlusions typical of shelf-dense spaces, and **representations** in which concepts are linear subspaces
rather than points. The last of these is our route to continual learning: because new concepts span
directions orthogonal to existing ones, the catalogue grows incrementally without overwriting what the
model already knows.

---

## Repositories

### 1. Network camera calibration

**[`vican`](https://github.com/sipg-isr/vican)** — A primal–dual bipartite pose-graph solver that
calibrates networks of thousands of cameras from ArUco marker observations in seconds instead of hours.
Its cost scales with the number of cameras rather than the number of images.

### 2. Edge computing

**[`vican_on_docker`](https://github.com/sipg-isr/vican_on_docker)** — Containerized VICAN, shipping the
object-calibration and camera-pose-estimation entry points plus ArUco and multi-camera configuration
helpers as a dependency-free service deployable on store hardware.

**[`boxes-WebUI`](https://github.com/sipg-isr/boxes-WebUI)** — The web layer of **boxes**, a fleet of
independent Dockerized inference services sharing a gRPC protocol (YOLOv8, TAPNext, LangSAM, CLIP,
Sentence-BERT, VGGT, MoGe-3, OpenCV), composable from a browser with no central orchestrator.

### 3. Behaviour understanding and scene perception

**[`Occlusion_HPE`](https://github.com/sipg-isr/Occlusion_HPE)** — Head pose estimation robust to occluded
faces, with tools to synthesize occlusions in 300W-LP, BIWI and AFLW2000, pre-trained models and an
evaluation protocol across occlusion levels.

**[`BlendMimic3D`](https://github.com/sipg-isr/BlendMimic3D)** — A Blender-built synthetic dataset for 3D
human pose estimation under occlusion (128 videos, 4 views, 3 subjects, 14 actions, Human3.6M format),
including a multi-person retail scene with shelves.
Project page: [blendmimic3d.github.io](https://blendmimic3d.github.io/BlendMimic3D/).

**[`ID-ControlNet`](https://github.com/sipg-isr/ID-ControlNet)** — Identity-preserving face inpainting, in
which a ControlNet-style branch injects ArcFace identity embeddings into a frozen latent diffusion model so
occluded faces are reconstructed as the right person.
*In retail:* both for faces partially occluded in ceiling views and for controlling what identity
information a model retains — central to privacy-compliant processing.

**[`cycling_subjective_safety`](https://github.com/sipg-isr/cycling_subjective_safety)** — **PCS-Net**, a
Siamese CNN that learns perceived safety of cycling environments from human pairwise image comparisons.
*Not a retail application, but it transfers directly:* the same learning-to-rank formulation can score
store layouts, aisles or storefronts by shoppers' perceived comfort, from images alone.

**[`Eyetracking-cycling-safety-perception`](https://github.com/sipg-isr/Eyetracking-cycling-safety-perception)** —
**EG-PCS-Net**, a Siamese Vision Transformer whose self-attention is supervised with human eye-tracking
saliency so the model looks where people look, released with the EG-PCS dataset of 13,623 pairwise
comparisons and 2,720 gaze maps ([Zenodo](https://doi.org/10.5281/zenodo.21242459)).
*Not a retail application, but it transfers directly:* gaze-aligned attention yields interpretable models
of what draws attention on a shelf, and an auditable answer to why a model decided what it did.

### 4. Representation and continual learning

**[`hyper`](https://github.com/gabmoreira/hyper)** — Hyperbolic embeddings for hierarchical recognition,
showing that the gains over Euclidean space come from the geometry of the hierarchy itself rather than from
curvature alone.

**[`visualsemantic-subspaces`](https://github.com/sipg-isr/visualsemantic-subspaces)** — A nuclear-norm
loss that aligns image embeddings with label subspaces in a Boolean lattice, representing negation as an
orthogonal complement and fixing a known weakness of CLIP-style models.

**[`subembed`](https://github.com/sipg-isr/subembed)** — Concepts embedded as linear subspaces, where
generality is dimension, hierarchy is inclusion and logical composition is linear algebra, learned
end-to-end through differentiable soft projection matrices.

**[`spectralguidance`](https://github.com/sipg-isr/spectralguidance)** — Training-free control of diffusion
models by projecting guidance signals onto a learned spectral basis, improving conditional accuracy by 37
percentage points on CIFAR-10 while sampling 4× faster.

---

## AI platform

Most of these algorithms also run as ready-to-use pipelines on our open-source platform:
**[visionist.sipg.tecnico.ulisboa.pt](https://visionist.sipg.tecnico.ulisboa.pt)**

---

## Funding

This work was carried out under the **PT Smart Retail** agenda, financed by the **Recovery and Resilience
Plan (PRR)** and by the **European Union — NextGenerationEU**.

<p align="center">
  <img src="prr.jpg" />
</p>
