---
layout: project
title: Linear Actuator - Homework 12
description: Statics class project
technologies: [python]
image: /assets/images/Linear_Actuator_Diagram.png
---

## Part 1 (Homework 4) – Rigid Beam

### Problem Definition

In Homework 4, we were tasked with designing a planar frame mechanism capable of lifting the **maximum possible load** to the **highest possible height** using a rigid bar and a linear actuator.

### Constraints and Objectives

- **2D workspace** of **150 cm length** and **50 cm height**  
- **3 pin supports**, two of which are mounted on the ground, and one **linear actuator**  
- Objective is to **maximize the height achieved by the rigid bar while lifting the largest possible load**.

### Design Degrees of Freedom

- Length and orientation of a **rigid bar**
- Selection and placement of a **linear actuator** from the Tolomatic catalog

### Solving the Problem

A computational approach was implemented in Python to analyze the static behavior of the mechanism. The analysis defined feasible ranges for two key design variables:

- The **distance** between the ground pivot and the linear actuator attachment point along the rigid bar.  
- The **angle** the rigid bar forms with the ground during operation.

These variables were discretized using a step size of **0.01**, and the algorithm iterated through all combinations to evaluate the torque balance about the pivot. For each configuration, the maximum load that could be supported under static equilibrium was calculated, and the actuator position and bar angle corresponding to this maximum load were recorded.

Several simplifying assumptions were made to streamline the analysis:

- The linear actuator force was assumed to act at **90° relative to the rigid bar**, maximizing the torque generated about the pivot.  
- The length of the rigid bar was fixed at **100 cm**, reducing the number of unknowns while remaining within the spatial constraints.  
- The **Tolomatic IMA actuator** was selected because it was the only actuator with complete performance data and fit within the design envelope when unextended.

### Final Design and Results

The analysis showed that the mechanism can support a **maximum load of 35.045 kN**. This occurs when:

- The linear actuator is attached at the **furthest allowable distance from the pivot (100 cm)**.  
- The rigid bar forms an angle of **16.7°** with the ground.

This result is physically intuitive. When the actuator force acts perpendicular to the rigid bar, increasing the distance from the pivot increases the moment arm and therefore the available torque to counteract the gravitational torque due to the load, allowing a larger weight to be lifted.

My code:
```python
    import numpy as np
    import math

def find_max_in_range(equation, x_range, theta_range, step_x, step_theta):
    x_vals = np.arange(x_range[0], x_range[1] + step_x, step_x)
    theta_vals = np.arange(theta_range[0], theta_range[1] + step_theta, step_theta)
    
    max_value = None
    max_x = None
    max_theta = None
    
    for x in x_vals:
        for theta_deg in theta_vals:
            value = equation(x, theta_deg)
            if (max_value is None) or (value > max_value):
                max_value = value
                max_x = x
                max_theta = theta_deg
    
    return max_value, max_x, max_theta

if __name__ == "__main__":
    # Directly use the full equation as a single expression
    # theta in degrees
    equation = lambda x, theta_deg: 36 * ((158.11 - x) * math.cos(math.radians(theta_deg)) + 30 * (math.sin(math.radians(theta_deg)) ** 2)) / 158.11

    x_range = (0, 100)         # x in cm
    theta_range = (16.7, 90)      # theta in degrees

    step_x = 0.01                 # cm, adjust for precision
    step_theta = 0.01             # degrees, adjust for precision

    max_value, max_x, max_theta = find_max_in_range(equation, x_range, theta_range, step_x, step_theta)
    print(f"Max value: {max_value:.6f} at x={max_x:.6f} cm, theta={max_theta:.6f} degrees")
```

## Part 2 (Homework 12) – Non-Rigid Beam Deflection

In Homework 12, the rigid bar from Part 1 is no longer assumed to be perfectly rigid. Instead, it is modeled as a structural beam that bends under the combined action of the lifted load and the applied actuator force. The objective of this analysis is to quantify the maximum beam deflection and select a beam design that limits vertical deflection to less than **2% of the beam length** while minimizing mass.

### Assumptions

The following assumptions were used in the beam analysis:

- The beam deforms elastically and remains in the linear bending regime.  
- The beam is modeled as a **cantilever**, fixed at the ground-mounted pivot and loaded at its free end.  
- Only **transverse load components** relative to the beam axis are considered.  
- The beam length is fixed at **1.0 m**, consistent with Part 1.  
- The combined actuator and load effects are conservatively represented as a single equivalent transverse point load, denoted \(F_\perp\).  
- The beam material is structural steel with:
  - Elastic modulus: **E = 200 GPa**  
  - Density: **ρ = 7850 kg/m³**

### Maximum Deflection Analysis

For a cantilever beam subjected to a transverse point load at its free end, the maximum vertical deflection at the tip is given by:

`δ_max = (F_perp * L^3) / (3 * E * I)`

where **I** is the second moment of area of the beam cross-section.

The design requirement specifies that the vertical deflection must be **less than 2% of the beam length**, giving an allowable deflection of:

`δ_allow = 0.02 * L = 0.02 m`

Rearranging the deflection equation yields the minimum required second moment of area:

`I_req = (F_perp * L^3) / (3 * E * δ_allow) ≈ 3.3 × 10⁻⁶ m⁴`

Any beam cross-section with a second moment of area greater than this value satisfies the deflection constraint.

### Beam Selection and Mass Efficiency

To meet the deflection requirement while minimizing mass, rectangular hollow steel sections were evaluated. These sections are efficient in bending because stiffness scales strongly with section height while mass scales linearly with cross-sectional area.

For a rectangular hollow section with outer width **b**, outer height **h**, and wall thickness **t**, the second moment of area and cross-sectional area are:

- `I = [b * h^3 − (b − 2t) * (h − 2t)^3] / 12`  
- `A = b * h − (b − 2t) * (h − 2t)`

Candidate beam sizes were evaluated using Python, and all sections satisfying the deflection criterion were identified. Among these candidates, the beam with the **smallest cross-sectional area** was selected to minimize mass.

The most mass-efficient beam meeting the deflection limit was a:

**60 mm × 150 mm × 3 mm rectangular hollow steel section**, oriented with the **150 mm dimension vertical**.

For this section:

- Second moment of area: `I ≈ 3.44 × 10⁻⁶ m⁴`  
- Cross-sectional area: `A ≈ 1.224 × 10⁻³ m²`  
- Mass per unit length: `≈ 9.6 kg/m`

The resulting maximum vertical deflection is:

`δ_max ≈ 0.0194 m`

which corresponds to **1.94% of the beam length**, satisfying the design requirement.

### Final Beam Design

The final beam design for the non-rigid mechanism is summarized below:

- **Beam length:** 1.0 m  
- **Cross-section:** 60 mm × 150 mm × 3 mm rectangular hollow section  
- **Material:** Structural steel  
- **Support condition:** Cantilevered at the ground pivot  
- **Maximum deflection:** 19.4 mm  
- **Performance:** Deflection remains below 2% of the beam length while minimizing beam mass

A drawing of the beam cross-section and its integration within the mechanism is shown below:

![Beam Deflection Diagram](/assets/images/Linear_Actuator_Diagram_Bending.jpeg)