---
layout: post
title: Bi-Weekly Advisor Meeting 27 of November 2025
date: 2025-11-27 11:12:00-0400
description: this is what included images could look like
tags: "bi-weekly" 
images:
  compare: true
  slider: true
---

# Draft

## Context


- Finished RL course
    - Use of Neural Networks for feature construction
    - Policy Gradient Algorithms
        - Actor-Critic Methods
        - Gaussian Policies for Continuous Action Spaces

- Search for papers combining HRC & Contact-rich & RL
    - No review papers found
    - Broadening to non-review articles showed 124 articles under SE  
    - Should look for HRC & Contact-Rich only, leaving RL out of it, maybe I'll find a decent review paper this way
    - Analysis of *Reinforcement Learning Based Variable Impedance Control for High Precision Human-robot Collaboration Tasks*

- Setting up containerized Isaac Lab
    - Settled on using Distrobox for ease of use and integration with host OS. Discarded Docker as I am not looking for isolation/sandboxing, I just need to use a software
    compatible with Ubuntu 22.04 (tested in Arch, launched but yielded many errors).
    - Set up [installation scripts](https://github.com/brunofavs/dotfiles/tree/main/scripts/Scripts/installation_scripts) to get all my dev
    tools, Isaac and Cuda working with one command. This will be useful whenever I need to work on one of the lab's server. And also for the
    sake of reproducibility if I ever need to reinstall.
    - Isaac requires Ubuntu 22.04 and Python 3.11. To ensure Python compatibility I used `pyenv` with the `pyenv-virtualenv` plugin. Up until now I
    just used `virtualenvwrapper`, but this tool does not allow the creation of virtual environments with different Python versions.

    There is a mistake in this sentence

