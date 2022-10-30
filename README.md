Vulkan Grass Rendering
==================================

**University of Pennsylvania, CIS 565: GPU Programming and Architecture, Project 5**

* Di Lu
  * [LinkedIn](https://www.linkedin.com/in/di-lu-0503251a2/)
  * [personal website](https://www.dluisnothere.com/)
* Tested on: Windows 11, i7-12700H @ 2.30GHz 32GB, NVIDIA GeForce RTX 3050 Ti

## Introduction

This is my first Vulkan project, which simulates grass movement in an outdoor environment based on the paper: [Responsive Real-Time Grass Rendering for General 3D Scenes](https://www.cg.tuwien.ac.at/research/publications/2017/JAHRMANN-2017-RRTG/JAHRMANN-2017-RRTG-draft.pdf)

This simulation is broken into three parts: Tessellation of the grass blade into some particular shape (in my case, a quadric knife shape), physics calcuations in a compute shader for grass movements, and culling unnecessary blades for performance. For the physics element of the simulation, the following three environmental forces are considered:
- Recovery: A force based on stiffness of a blade that counteracts external forces of gravity and wind
- Gravity: Gravitational force from the ground
- Wind: Force applied by wind and air
- Result Validation (to clamp the recalculated blade shape's length and position)

![](img/diGrass2.gif)

## Core Features Implemented

### Grass Shape Tessellation

At the initial stage of the project, there is only a patch of dirt with no visible grass on it. This is because all grass blades initially are represented by one vertex. In order for grass blades to appear, we must tessellate the vertex into a surface curve that represents the grass blade's shape. Based on Real-Time Grass Rendering, grass can be suggested to follow these possible curves:

<img src="https://github.com/dluisnothere/Project5-Vulkan-Grass-Rendering/blob/main/img/bladeShape.png" width="400">

For my project, I chose #3, the quadric "blade" shape for my grass.

Furthermore, each grass blade is controlled by a series of control points illustrated by the following diagram:

![](img/bladeModel.png)

### Environmental Forces

### Culling

## Results

## Performance Analysis

## Bloopers! :)
