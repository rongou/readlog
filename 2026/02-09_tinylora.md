# Learning to Reason in 13 Parameters

* Authors: Meta
* Link: https://arxiv.org/abs/2602.04118
* Date: 2026-04-04

## Notes

* LoRA: $W' = W + AB$, $W$ frozen, $A$ and $B$ trainable, rank $r$
* LoRA-XS: $W' = W + U \Sigma R V^{\top}$, $U$, $\Sigma$, $V$ truncated SVD of $W$, only $R$ trainable, learning to
  recombine the dominant singular directions of $W$
* TinyLoRA: $W' = W + U \bigl(\sum_{i=1}^{u} v_i P_i\bigr) V^{\top}$, $R$ replaced by a linear combination of $u$ fixed
  random matrices $P_i$
* RL works better than SFT because $u$ underfits SFT

## Ideas

* random matrices to expand signals to make them linearly separable
