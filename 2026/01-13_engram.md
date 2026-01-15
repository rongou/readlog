# Conditional Memory via Scalable Lookup: A New Axis of Sparsity for Large Language Models

* Authors: DeepSeek
* Link: https://www.arxiv.org/abs/2601.07372
* Date: 2026-01-12

## Notes

* Mixture-of-Experts (MoE) scales capacity via conditional computation
* introduce conditional memory as a complementary sparsity axis
* sparse lookup operations to retrieve static embeddings for fixed knowledge
* Engram: a conditional memory module grounded in the classic N-gram structure but equipped with modern adaptations such
  as tokenizer compression, multi-head hashing, contextualized gating, and multi-branch integration
* Sparsity Allocation problem: given a fixed total parameter budget, how should capacity be distributed between MoE
  experts and Engram memory?
* U-shaped scaling law
* Engram employs deterministic IDs to enable runtime prefetching, overlapping communication with computation
* offloading a 100B-parameter table to host memory incurs negligible overhead (< 3%)
* Tokenizer Compression: collapses raw token IDs into canonical identifiers based on normalized textual equivalence
* Multi-head hashing: K distinct hash heads for each N-gram order, concatenating all retrieved embeddings
* Context-aware Gating: utilize the current hidden state as a dynamic Query, while the retrieved memory serves as the
  source for both Key and Value projections; finally, a short, depthwise causal convolution

## Ideas

* Content addressable memory? Deterministic MoE?
