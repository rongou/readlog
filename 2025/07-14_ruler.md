# RULER: Easy Mode for RL Rewards

* Author: Kyle Corbitt @ OpenPipe
* Link: https://openpipe.ai/blog/ruler
* Date: 2025-07-11

## Notes

* RULER (Relative Universal LLM-Elicited Rewards)
* Beats OpenAI o3, 3/4 manual RL on Qwen 2.5
* LLM-as-judge ranks N trajectories relatively, no need for calibration

Default Rubric:

* A trajectory that achieves its goal should always get a significantly higher score than a trajectory that does not
  achieve its goal.
* A trajectory that achieves its goal more efficiently (eg. by avoiding unproductive detours) should get a higher score
  than a trajectory that achieves its goal less efficiently.
* If one trajectory is only slightly better than another, the difference in scores should be small. If it is
  significantly better, the difference in scores should be large.
* You may give some partial credit for a trajectory that makes progress towards its goal but does not complete it.

## Ideas

In a multi-LLM inference setting, we can apply a similar idea to rank the outputs of multiple LLMs.
