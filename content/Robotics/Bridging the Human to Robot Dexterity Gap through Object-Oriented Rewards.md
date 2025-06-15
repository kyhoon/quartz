---
url: https://arxiv.org/abs/2410.23289
tags:
  - robotics
  - todo
---
## Abstract

Training robots directly from human videos is an emerging area in robotics and computer vision. While there has been notable progress with two-fingered grippers, learning autonomous tasks for multi-fingered robot hands in this way remains challenging. A key reason for this difficulty is that a policy trained on human hands may not directly transfer to a robot hand due to morphology differences. In this work, we present HuDOR, a technique that enables online fine-tuning of policies by directly computing rewards from human videos. Importantly, this reward function is built using object-oriented trajectories derived from off-the-shelf point trackers, providing meaningful learning signals despite the morphology gap and visual differences between human and robot hands. Given a single video of a human solving a task, such as gently opening a music box, HuDOR enables our four-fingered Allegro hand to learn the task with just an hour of online interaction. Our experiments across four tasks show that HuDOR achieves a 4x improvement over baselines. Code and videos are available on our website, [this https URL](https://object-rewards.github.io/).

