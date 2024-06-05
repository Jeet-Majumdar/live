---
title: Installing
tags: [Import-502f]
created: '2023-09-30T09:29:15.348Z'
modified: '2023-10-13T07:27:55.013Z'
---

## Installing

`pip install -e .`

## Accessing modules of TrajAnalyser

```python
import numpy as np
import TrajAnalyser as ta

filename = 'system_2pt.2pt.lammps'
traj = ta.Trajectory(
    filename=filename, 
    trajectory_format='lammps_internal',
    atomic_indices='read_from_trajectory',
    length_unit='Angstrom', 
    time_unit='fs',
    frame_start=20,
    frame_stop=25,
    isNPT=False
    )

# First frame positions / velocities
frame0 = next(traj)
frame0_positions = frame0.positions
frame0_velocities = frame0.velocities

# Second frame positions
frame1 = next(traj)
frame1_positions = frame1.positions
frame1_velocities = frame1.velocities

```

## Extract the full trajectory details in numpy array
> This consumes the entire trajectory. 
> So to reuse traj object, reload the trajectory file again with 'Trajectory'.
> Cell vectors are returned in the format of MDAnalysis u.trajectory.ts.triclinic_dimensions 
> where u is the MDAnalysis Universe object

```python
positions, cell = ta.positions2array(traj)  
velocities, cell = ta.velocities2array(traj)
```
