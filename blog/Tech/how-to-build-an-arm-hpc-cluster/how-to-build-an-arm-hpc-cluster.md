---
title: "构建基于ARM的超算集群"
date: "2019-08-26"
categories: 
  - "cloud-infra"
  - "devices"
  - "hpc"
---

# Background

Nowadays ARM-based servers are being applied to many scenarios, so we are going to port some workload to the minimal ARM SoC with NVIDIA GPU to evaluate the feasibility of production usage.

# Preparation

Hardware: Legacy ARM SoC boards, e.g. Raspberry Pi, Beagle Board, NVIDIA Jetson(GPU) and standard ARM-based servers.

MPI: OpenMPICH and MPICH2.

Share Storage: ARM-based Glusterfs with pNFS.

Application: ANY

# Build/Installation

Ref:

\[1\] [https://developer.arm.com/solutions/hpc](https://developer.arm.com/solutions/hpc)
