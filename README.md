# PT Smart Retail — Contributions from IST / IST-ID / ISR

**Signal and Image Processing Group (SIPG), Institute for Systems and Robotics (ISR),
Instituto Superior Técnico, Universidade de Lisboa**

This page collects the open-source software, datasets and research code produced by
**IST**, **IST-ID** and **ISR** within the **PT Smart Retail** mobilizing agenda.

---

## About the project

**PT Smart Retail — *Portugal as a reference for the new generation of smart and autonomous retail*** is a
mobilizing agenda for business innovation funded by the Portuguese Recovery and Resilience Plan (PRR) and
led by **[Sensei](https://www.sensei.tech)**. It brings together industrial, technological, scientific and
end-user partners along the whole retail value chain to design, demonstrate and industrialize a new
generation of autonomous retail formats — from full autonomous stores to modular pods and smart cabinets —
together with the supporting technologies that make them possible: smart shelves and sensors, interactive
surfaces, seamless payments, connectivity, robotics, and the computer vision and machine learning stack
that lets a store see and understand what happens inside it.

## Contribution of IST / IST-ID / ISR

An autonomous store is an unusually dense sensing infrastructure — hundreds to thousands of cameras,
crowded scenes, severe occlusions, a large and constantly changing product catalogue — under strict
privacy and energy budgets. Our contribution is the perception and representation stack that makes this
tractable, and it maps onto the four groups of repositories below. **Calibration** comes first: VICAN
brought the calibration of networks with thousands of cameras and millions of images down from hundreds of
hours to a few seconds, which is what makes a densely instrumented store deployable at all. **Edge
computing** turns those algorithms into services: containerized, independently deployable inference boxes
and a web layer that let non-specialists compose and run vision pipelines on store hardware, at the edge or
in the cloud.

On top of that infrastructure we work on **behaviour understanding and scene perception** — body and head
pose estimation robust to the self-, object- and out-of-frame occlusions typical of shelf-dense spaces,
synthetic benchmarks to train and evaluate it, identity-preserving face reconstruction, and models of how
people subjectively perceive a space — and on **representation and continual learning**, where concepts are
embedded as linear subspaces rather than points. There, hierarchy is subspace inclusion, generality is
dimension, and logical composition — including negation, which CLIP-style models handle poorly — is linear
algebra. Because new concepts span directions orthogonal to existing ones, the catalogue can grow
incrementally without overwriting what the model already knows, which is the property a retail inventory
that changes every week actually needs. The same geometric view carries over to the controllable generative
models used to synthesize training data.

---

## Repositories

### 1. Network camera calibration

#### [`vican`](https://github.com/sipg-isr/vican)

Reference implementation of **VICAN — Very Efficient Calibration Algorithm for Large Camera Networks**
(ICRA 2024). A primal-dual bipartite pose-graph optimization solver that jointly calibrates a marker
object and estimates the extrinsic poses of every camera in a network from ArUco marker observations.
Complexity scales with the number of cameras rather than with the number of images, which brings the
calibration of thousands of cameras down to seconds. Includes datasets and Google Colab test scripts.

> *G. Moreira, M. Marques, J. P. Costeira, A. Hauptmann, "VICAN: Very Efficient Calibration Algorithm
> for Large Camera Networks", IEEE ICRA 2024.*

### 2. Edge computing and algorithms-as-a-service

#### [`vican_on_docker`](https://github.com/sipg-isr/vican_on_docker)

Containerized deployment of VICAN. Ships the object-calibration and camera-pose-estimation entry points
(`object_calib.py`, `pose_est.py`) inside a Docker image, plus helper utilities for ArUco ID detection and
multi-camera configuration generation. Turns the calibration algorithm into a reproducible, dependency-free
service that can be provisioned on store hardware or at the edge in seconds.

#### [`boxes-WebUI`](https://github.com/sipg-isr/boxes-WebUI)

The web layer of **boxes**, a fleet of independent Dockerized AI inference services that talk to each other
over a shared gRPC protocol, with no central orchestrator. Each "box" is a self-contained container, and the
WebUI exposes them declaratively over HTTP so that non-specialists can compose pipelines from a browser.
Bundled boxes include YOLOv8 (detection and tracking), TAPNext (point tracking), LangSAM (text-guided
segmentation), CLIP and Sentence-BERT (multimodal embeddings), VGGT (3D reconstruction), MoGe-3
(monocular depth and normals) and OpenCV primitives. This is the engine behind the group's
algorithms-as-a-service platform and its IoT/edge deployments.

### 3. Behaviour understanding and scene perception

#### [`Occlusion_HPE`](https://github.com/sipg-isr/Occlusion_HPE)

Head pose estimation robust to occluded faces, together with tools to synthesize realistic occlusions in
face datasets (300W-LP, BIWI, AFLW2000). Provides training scripts, inference notebooks, pre-trained
models and an evaluation protocol across occlusion levels. Head orientation is a direct proxy for customer
attention — which shelf, which product, for how long — in exactly the crowded, partially occluded views
that store camera networks produce.

> *J. Celestino, M. Marques, J. C. Nascimento, J. P. Costeira, "2D image head pose estimation via latent
> space regression under occlusion settings", Pattern Recognition, 137:109288, 2023.*
>
> *J. Celestino, M. Marques, J. C. Nascimento, "Latent Embedding Clustering for Occlusion Robust Head Pose
> Estimation", IEEE Int. Conf. on Automatic Face and Gesture Recognition (FG), pp. 1–9, 2024.*

#### [`BlendMimic3D`](https://github.com/sipg-isr/BlendMimic3D)

A synthetic dataset built in Blender for 3D Human Pose Estimation under occlusion: 128 videos, 4 camera
views, 3 subjects and 14 actions, with pixel-perfect 2D/3D keypoints, per-keypoint occlusion flags and full
camera calibration, organized in Human3.6M format. Scenarios range from simple environments to a
multi-person retail scene with shelves, covering self-occlusion, object occlusion and out-of-frame
occlusion. Project page: [blendmimic3d.github.io](https://blendmimic3d.github.io/BlendMimic3D/).

> *F. Lino et al., "3D Human Pose Estimation with Occlusions: Introducing BlendMimic3D Dataset and
> GCN Refinement", CVPR Workshops 2024.*

#### [`ID-ControlNet`](https://github.com/sipg-isr/ID-ControlNet)

Identity-preserving face inpainting. A ControlNet-style branch injects identity embeddings from a frozen
face recognition encoder (ArcFace) into a pretrained latent diffusion inpainting model, so that occluded
facial regions are reconstructed as *that* person rather than drifting to an average face. Only the small
control branch is trained; the objective combines reconstruction, identity-consistency and cycle/triplet
losses. Evaluated on CelebA-HQ, FFHQ and the new E-Mask dataset of identity-critical occlusions.
In a retail setting this matters twice over: reconstructing partially occluded faces captured by ceiling
cameras, and — read in reverse — controlling exactly what identity information a generative model retains,
which is central to privacy-compliant processing.

> *J. Santos, C. Santiago, M. Marques, "Face Inpainting with Identity Preserving Latent Diffusion Models",
> arXiv:2605.16696, 2026.*

#### [`cycling_subjective_safety`](https://github.com/sipg-isr/cycling_subjective_safety)

**PCS-Net**, a Siamese CNN that learns *perceived* safety of cycling environments directly from street
imagery, trained on human pairwise image comparisons (with ties) under a multi-loss framework. It lets
planners rank streets and neighbourhoods by how unsafe cyclists feel there, without surveys or in-loco
interviews.
*Not a retail application as such* — but the underlying machinery transfers directly to a retail scenario:
the same pairwise-comparison, learning-to-rank formulation can be trained on shoppers' subjective
judgements to score store layouts, aisle designs, shelf arrangements or storefronts by perceived
comfort, safety or attractiveness, from images alone and at store-network scale.

> *M. Costa, M. Marques, C. L. Azevedo, F. W. Siebert, F. Moura, "Which Cycling Environment Appears
> Safer? Learning Cycling Safety Perceptions From Pairwise Image Comparisons", IEEE T-ITS, 26(2), 2025.*

#### [`Eyetracking-cycling-safety-perception`](https://github.com/sipg-isr/Eyetracking-cycling-safety-perception)

**EG-PCS-Net**, a Siamese Vision Transformer that jointly optimizes pairwise safety classification, ranking
and *attention–gaze alignment*: the transformer's self-attention is supervised with saliency maps derived
from human eye-tracking, via a KL-divergence loss, so the model looks where people actually look. Comes
with the EG-PCS dataset — 13,623 pairwise comparisons, 2,720 gaze maps, 251 survey participants and 26
laboratory eye-tracking sessions ([Zenodo](https://doi.org/10.5281/zenodo.21242459)).
*Again not retail-specific* — but gaze-supervised attention is immediately applicable in a retail scenario:
aligning model attention with shopper gaze yields interpretable models of what actually draws attention on
a shelf or a display, and gives an auditable answer to *why* a vision model reached a given conclusion —
a requirement for deploying AI in stores.

> *L. Perdigão, M. Costa, R. Santiago, M. Marques, "Learning to See Like Humans: Gaze-Aligned Cycling
> Safety Prediction", IEEE ITSC 2026.*

### 4. Representation and continual learning

#### [`visualsemantic-subspaces`](https://github.com/sipg-isr/visualsemantic-subspaces)

Training code for **Learning Visual-Semantic Subspace Representations** (AISTATS 2025). A nuclear-norm
loss aligns image embeddings with binary label vectors inside a *subspace Boolean lattice*: each label is a
subspace projector, an image lands in the intersection of the subspaces of its concepts, and **negation is
the orthogonal complement** of a subspace. This gives hierarchies, overlaps and logical operators directly
in the embedding space, and fixes a known weakness of CLIP-style models, which struggle with negation
("men with a beard but *without* glasses"). For a product catalogue, it means errors stay semantically
meaningful — red wine confused with white wine, not with a cardigan.

> *G. Moreira, M. Marques, J. P. Costeira, A. G. Hauptmann, "Learning Visual-Semantic Subspace
> Representations", AISTATS 2025.*

#### [`subembed`](https://github.com/sipg-isr/subembed)

Code for **Native Hierarchical and Compositional Representations with Subspace Embeddings** (KDD 2026).
Concepts are embedded as *linear subspaces* rather than points: generality is the subspace dimension,
hierarchy is subspace inclusion, and logical composition emerges from linear algebra (conjunction =
intersection, disjunction = span, negation = orthogonal complement). A differentiable parameterization with
soft projection matrices lets the effective dimension of each concept be learned by gradient descent, while
staying compatible with optimized Euclidean vector search. This is the group's main route to **continual
learning**: new concepts span new orthogonal directions or nest inside broader ones, so the knowledge base
grows incrementally without overwriting what is already there. Evaluated on WordNet reconstruction and link
prediction, HyperLex and SNLI.

> *G. Moreira, Z. Marinho, M. Marques, J. P. Costeira, C. Xiong, "Native Hierarchical and Compositional
> Representations with Subspace Embeddings", ACM SIGKDD (KDD) 2026.*

#### [`spectralguidance`](https://github.com/sipg-isr/spectralguidance)

Code for **Spectral Guidance for Flexible and Efficient Control of Diffusion Models** (ICML 2026). As data
is corrupted by noise, high-frequency detail disappears while coarse semantics persist; these surviving
features are characterized as the leading singular functions of a conditional expectation operator across
diffusion timesteps. Learning them offline, with orthogonality constraints, builds a time-indexed
low-dimensional basis onto which arbitrary guidance signals — class labels, CLIP embeddings, segmentation
masks — can be projected at inference time. No task-specific denoiser retraining and no denoiser gradients
during sampling: **+37 percentage points** in conditional accuracy over the strongest training-free
baselines on CIFAR-10, with **4× faster** sampling. The spectral analysis also reveals a phase transition
that pinpoints when guidance is most effective. Useful for controllable synthetic data generation — the same
role the Blender simulator plays for calibration and behaviour data.

> *G. Moreira, M. Marques, J. P. Costeira, C. Xiong, "Spectral Guidance for Flexible and Efficient
> Control of Diffusion Models", ICML 2026.*

---

## AI platform

Most of these algorithms are also available as ready-to-run pipelines on our open-source
algorithms-as-a-service platform: **[visionist.sipg.tecnico.ulisboa.pt](https://visionist.sipg.tecnico.ulisboa.pt)**

---

## Funding

This work was carried out under the **PT Smart Retail** mobilizing agenda (Notice N.º 02/C05-i01/2022),
financed by the **Recovery and Resilience Plan (PRR)** and by the **European Union — NextGenerationEU**.

![Funded by the Recovery and Resilience Plan (PRR), República Portuguesa and the European Union — NextGenerationEU](prr.jpg)
