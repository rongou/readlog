# Cosmos 3: Omnimodal World Models for Physical AI

* Authors: NVIDIA
* Link: https://research.nvidia.com/labs/cosmos-lab/cosmos3/technical-report.pdf
* Date: 2026-06-22

## Notes

* two fundamentally coupled capabilities: understanding and generation
* omnimodal world models that jointly model language, image, video, audio, and action for both understanding and
  generation
* encoders:
    * image and video: ViT for understanding, VAE for generation
    * audio: VAE for generation
    * action: ego poses for the agent’s main observation frame, effector poses for the agent’s effectors, and grasp
      states for the manipulation state; use domain-aware input and output projection layers with separate weight
      matrices for each embodiment domain
* token arrangement: an autoregressive (AR) subsequence followed by a diffusion (DM) subsequence
    * AR subsequence: language tokens and video and image tokens embedded by the ViT encoder
    * DM subsequence: video and image tokens from the VAE encoder, audio and action tokens; clean conditioning tokens
      are placed before noisy diffusion tokens; ordered by vision, audio, and action modality
* Mixture-of-Transformers (MoT): AR causal self-attention, DM full bidirectional attention; reasoner on AR subsequence
  only, DM on both subsequences
* position embeddings: 3D Multimodal RoPE (MRoPE) with absolute temporal indexing
    * AR tokens: original 3D MRoPE design, language tokens reduce to 1D RoPE (t = h = w)
    * DM tokens: language t = h = w, video tokens vary on all 3 axes, action and audio tokens use temporal coordinates
      only (h = w = 0)
    * FPS modulation maps frame indices to scaled temporal positions so that equal real-world durations occupy equal
      position ranges at 16, 24, and 30 FPS, where 24 FPS is base frame-per-second
    * a fixed temporal gap (15000) between the autoregressive and diffusion subsequences
    * absolute temporal modulation: temporal steps per second (TPS); video TPS = frame rate / 4, audio TPS = 48000 /
      1920 = 25, action TPS = sampling frequency
* model variants: edge (2B), nano (8B), super (32B)
* data: reasoner is trained on paired vision-language data, generator is trained on images, videos, audio, and actions
  using reconstruction-based objectives
* reasoner data
    * pre-training: dominated by image–text and text-only data
    * SFT: 50% video-text
* semantic deduplication: image-text and text-only conversations are embedded using Qwen3-VL-Embedding-8B; video-text
  conversations are embedded using the Perception Encoder PE-Core-G14-448
* AI-judge quality filtering: faithfulness, completeness, correctness
* SFT data:
    * general spatial understanding: 2D and 3D grounding, real-world spatial understanding and grounding,
      simulator-grounded embodied spatial reasoning
    * general temporal understanding: temporal event understanding, physical plausibility judgment, structured
      spatiotemporal scene upsampling
    * autonomous vehicle (AV): action CoT, temporal event localization, 3D vehicle grounding
    * robotics and embodied AI: robot action Chain-of-Thought, embodied reasoning, healthcare robotic surgery
      understanding
    * smart infrastructure: warehouse spatial intelligence, dense pedestrian localization, traffic and anomaly reasoning
* generator data: multi-stage curriculum that introduces new modalities incrementally over the course of training,
  starting with images, videos, and audio during pre-training, and later incorporating actions and interleaved
  multimodal content during mid-training
* image and video:
    * collecting raw data and performing pre-processing
    * computing embeddings and conducting deduplication
    * categorizing samples and applying basic filtering
    * annotating data
    * grouping samples into training-ready shards based on their resolution and duration
* caption: structured JSON annotation format - subjects, background, lighting, aesthetics, cinematography, temporal
  dynamics; fine-tuned two Qwen3-VL-8B models on structured annotation data
* caption-quality benchmark for both images and videos: precision is evaluated directly against the source media to
  penalize hallucinations: a VLM decomposes the generated caption into atomic claims and verifies whether each claim is
  visually supported by the image or video itself; recall measures comprehensiveness and relies on human-curated ground
  truth
* audio pre-training: use Qwen3-Omni-Captioner to generate synthetic audio descriptions
* audio mid-training: keep speech only when it is synchronized with a visible face, remove off-screen speech from
  non-speech examples, and remove non-instrumental BGM when it would dominate the target audio
* paired text-video-action data during mid-training, four physical-AI pillars: egocentric motion, robotics, autonomous
  vehicles, and camera motion
* training:  two main phases - the Reasoner is pre-trained on large-scale image–text and video–text corpora, and
  fine-tuned on a curated Physical AI mixture; the trained Reasoner weights are then used to initialize the Generator,
  which is trained using a progressive multi-stage curriculum, begins with large-scale image, video, and audio
  pre-training, followed by mid-training that gradually introduces action and transfer data. Finally, the model is
  post-trained on smaller, carefully curated Physical AI datasets
* training: sequences of at most 16k tokens, with per-sample limits of 2048 image tokens and 8192 video tokens;
  square-root normalized per-token loss weighting to balance the contributions of short and long sequences
* data infrastructure: lance-based incremental curation
* training infrastructure:
    * data loader: ingest, on-the-fly augmentation, tokenize text, pack variable-length samples into batches; runs
      asynchronously in parallel worker processes and prefetches batches onto the device via a pinned-memory staging
      buffer
    * distributed training: a combination of Hybrid Sharded Data Parallelism (HSDP) and Context Parallelism (CP)
    * training loop: TorchTitan
    * checkpoint saving: configurable cadence; asynchronous, off-critical-path persistence mechanism
* prompt upsampler converts user requests into this structured format for generation

## Ideas

