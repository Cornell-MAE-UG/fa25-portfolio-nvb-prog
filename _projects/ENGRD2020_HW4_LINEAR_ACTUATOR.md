---
layout: project
title: Linear Actuator - Homework 4
description: Statics class project
technologies: [python]
image: /assets/images/Linear_Actuator_Diagram.png
---

Problem Statement: Given a 2D design space of 150cm long and 50cm tall, a rigid bar of a fixed length (your choice), 3 pin supports of which two need to be mounted on the ground and a linear actuator (pick from this online catalog, use max force values only), design a frame/mechanism to lift the maximum possible weight to the highest possible height. Assume all the supports and bar/actuator are rigid.

This is how I solved the problem:

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
This code works by essentially giving ranges of possible values for (1) the distance the linear actuator can be from the pivot and (2) the angle that the rigid bar can make with the ground. It then iterates for tons of values with a step size of 0.01 between the different ranges to find the largest weight that can be supported and figure out at what x distance from the pivot the linear actuator is for that to occur and what angle the rigid bar ends up making with the ground. 

I made a couple assumptions in this problem to make my calculations easier. First, I assumed that the linear actuator was at a 90-degree angle with the rigid bar as this will provide the most torque about the pivot point to push the weight up. I also picked a fixed length of the bar as 100m to reduce unknowns. I picked the IMA actuator because the PDF seemed to only have information about that actuator and it seemed to fit in the design when unextended. 

My code told me that the max weight that could be lifted was 35.045kN with the linear actuator at 100m away from the pivot, meaning right on the edge. The angle that the rigid bar forms with the ground is 16.7 degrees. This makes sense given my assumptions becasue if the force is perpendicular to the moment arm then the longer the moment arm the more torque generated that can combat the torque that gravity is exerting on the bar, allowing the most weight to be lifted. 