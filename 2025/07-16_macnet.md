# Scaling Large Language Model-based Multi-Agent Collaboration

* Authors: Tsinghua & Peng Cheng Laboratory
* Link: https://arxiv.org/abs/2406.07155
* Date: 2025-03-17
* Conference: ICLR 2025

## Notes

* Directed acyclic graph (DAG) to organize multi-agent collaborative network (MacNet)
* LLM agents: context-aware memory, tool use, procedural planning
* Multi-agent collaboration: iterative reflection and refinement within an interactive environment
* Fixed topology: chain, star, tree, mesh, layer, and random
* Bipartition of agents into actor and critic, nodes are actors, edges are critics
* Short and long-term memory: only the final artifact is passed to the next agent, not the entire history
* Collaborative scaling law: up to 1000 agents, performance improves with more agents in sigmoid manner

## Ideas

This sounds a bit like AB-MCTS (https://arxiv.org/abs/2503.04412). Maybe they can somehow be combined?
