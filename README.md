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
- **Note that the paper also specifies Collision force, but we don't take that into account here**

![](img/diGrass2.gif)

## Core Features and Results

### 1. Grass Shape Tessellation

At the initial stage of the project, there is only a patch of dirt with no visible grass on it. This is because all grass blades initially are represented by one vertex. In order for grass blades to appear, we must tessellate the vertex into a surface curve that represents the grass blade's shape. Based on Real-Time Grass Rendering, grass can be suggested to follow these possible curves:

<img src="https://github.com/dluisnothere/Project5-Vulkan-Grass-Rendering/blob/main/img/bladeShape.png" width="600">

For my project, I chose #3, the quadric "blade" shape for my grass.

Furthermore, each grass blade is controlled by a series of control points illustrated by the following diagram:

![](img/blade_model.jpg)

- `v0`: fixed base point of a grass blade
- `v1`: this point is "interpolated"/calculated as a response to `v2` and `v0`
- `v2`: this point gets moved based on the environmental forces
- `up`: The blade's up vector which sprouts from the ground upward in the Y direction. This is the same as the normal of the pane.

Each of the above points are represented as `vec4s` where the element _w_ (fourth element) represents the following:

- `v0.w - theta`: angle of rotation of the grass blade. This is different for every blade of grass which will give variety to the grass patterns.
- `v1.w - blade height`: height of the grass blade
- `v2.w - blade width`: width of the base of the blade
- `up.w - blade stiffness`: stiffness of the blade/resistance towards the environmental forces

Here is the simulation without any forces:

![](img/grass.png)

### 2. Environmental Forces

There are three environmental forces that impact the grass blade:

- Recovery: A force based on stiffness of a blade that counteracts external forces of gravity and wind
- Gravity: Gravitational force from the ground
- Wind: Force applied by wind and air

#### Gravity

With only gravity active, all grass blades will collapse to the ground. Gravitational force is a combination of `frontal gravity` and `downward gravity`. Environmental gravity acts along a vector of influence which is orthogonal to the width of the blade of the grass (along the face forward direction of the grass). Here is an image of the result with just gravity:

![](img/gravity.png)

#### Resistance

Resistance factor is calculated by taking into account a stiffness constant defined by each grass blade. We essentially render the blade between the fully upstanding position and the collapsed ground position.

![](img/resistance.png)

#### Wind

Wind factor is calculated by using a procedural sinusoidal wave and fbm noise to calculate some noise factor applied to the vector displacement.

![](img/wind.gif)

### 3. Culling

#### Rotational Culling 

Rotational Culling removes blades such that its face-forward is exacty orthongal to the camera view vector towards the blade. This is because a blade has no thickness, so at that angle, there is no reason to send it to the render pipeline.

![](img/rotation.gif)

#### Frustum Culling

Frustum Culling removes blades that are outside of your viewport. With my implementation, there is a slight border where I remove blades within the frame as well so you can see the effect more clearly.

![](img/frustum.gif)

#### Distance Culling

Distance Culling removes blades that are outside of a certain distance.

![](img/distance.gif)

## Performance Analysis

**Performance Measurements vs. Number of Blades**

![](img/fpsNumBlades.png)

Based on the measurement of FPS above, increasing the number of blades will decrease FPS, as expected.

**Performance Measurements vs. Different Culling Methods over an increasing number of blades**

![](img/lastChart.png)

This chart above is a comparison of performance between NO culling, Frustum Culling, Orientation Culling, Distance Culling, and ALL culling. However, the results are a little bit difficult to generalize. This is because each of the three culling methods work best for different scenarios/setups. For example, Frustum culling works best when you are really zoomed in on a patch of few blades. Orientation culling can be more accurately understood by the graph above since all the grass blades are oriented randomly. Distance culling works best when you can compare the results between being far away from the scene vs. being close up. However, it's easy to see from above that having ALL culling turned on is generally better than having No Culling at all.

## Bloopers! :)
