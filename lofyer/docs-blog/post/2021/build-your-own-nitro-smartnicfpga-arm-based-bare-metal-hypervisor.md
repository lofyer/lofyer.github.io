---
title: "Build Your Own Nitro SmartNIC(FPGA/ARM-based Bare Metal Hypervisor)"
date: "2021-02-25"
categories: 
  - "cloud"
  - "fpga"
  - "raspberrypi-play"
---

# 0\. BackGround

BlueField 1/2

AWS Nitro

Aliyun X-Dragon MOC

OpenBMC

# 1\. Functions Spec

## 1.1. Out-of-Band Management

Out-of-Band Management is not a problem if:

1. Your standard server has got a BMC.
2. Your have got your own server supply chain to custom your server very careful, since you have to transfer some functions of the BMC and BIOS to the smartnic.

### 1.1.1. Power Management

### 1.1.2. Physical Host Communication

## 1.2. Device Emulation

### Storage

What we can simulate for now: iSCSI(SCSI), NVMe, CDROM, VirtIO?

### Network

## 1.3. Hypervisor Simulation

# 2.1. Proof of Concept

## 2.1. ARM or X86

At first let's build a OpenBMC-based prototype.

This is what you need:

A laptop;

A Raspberry Pi;

A Video Capture Card;

A keyboard and mouse.

## 2.2. ARM-FPGA SoC
