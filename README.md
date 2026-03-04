# RL Autonomous Vehicle Trajectory Tracking (nuScenes v1.0-mini)

This project trains and evaluates a reinforcement learning (RL) agent for **trajectory tracking** using a **kinematic bicycle model** on **nuScenes v1.0-mini** scenes. The agent learns to follow reference trajectories (including CAN-derived signals when available) and is compared against classical baselines such as **Pure Pursuit**.

## Key Features
- **Dataset:** nuScenes `v1.0-mini` (10 scenes)
- **Dynamics:** Kinematic Bicycle Model (continuous control)
- **Agent:** PPO (Stable-Baselines3) with **VecNormalize**
- **Baselines:** Zero-action + Pure Pursuit
- **Deliverables:** 
  - Per-scene **GIFs** showing agent tracking behavior (Reference vs PPO overlay)
  - Reward curves over timesteps and **per-scene reward plots**
  - Summary tables (return, position error, etc.)
    

## Problem Setup

### State / Observation 
The environment returns a 6D observation vector:
- `dx, dy`: position error relative to reference (meters)
- `dv`: speed error relative to reference (m/s)
- `dpsi`: heading error relative to reference (rad)
- `prev_acc`: previous acceleration command (m/s²)
- `yaw_rate`: current yaw rate (rad/s)


### Action (2D)
Continuous action:
- `a_cmd`: longitudinal acceleration command (m/s²)
- `delta_cmd`: steering command (rad)

### Reward Design (Tracking + Comfort + Progress)
Reward combines:
- Tracking penalties: position, speed, heading errors  
- Comfort penalties: jerk, lateral acceleration, yaw rate, control effort  
- Progress term gated by distance-to-path (prevents “going fast while off-path”)  
Robust penalties (Huber-style) and clipping are used to stabilize learning.


## Training
- Algorithm: **PPO (SB3)**
- Normalization: **VecNormalize** (obs normalization + reward normalization during training)
- Multi-scene training: scenes are sampled per episode
- Evaluation: deterministic PPO policy with frozen normalization stats


## Results Summary (example from held-out scenes)
On a held-out split (3 test scenes), PPO achieved lower mean tracking error than Pure Pursuit:

- **Pure Pursuit mean position error:** ~3.87 m  
- **PPO (VecNorm) mean position error:** ~3.49 m  

(Exact values may vary slightly across runs due to random seeds and scene difficulty.)


## Visualizations Included
- **Evaluation return vs timesteps** (from `evaluations.npz`)
- **Per-scene reward curves** (reward vs timestep)
- **Per-scene GIFs**: Reference vs PPO overlay (one GIF per scene)

Outputs are saved into folders such as:
- `gifs_all_scenes/`
- `reward_curves_all_scenes/`
- `logs_*/` (training logs, VecNormalize stats, evaluations)


## How to Run
1. Open the notebook:
   - `RL_Autonomous_Vehicle_Agent.ipynb`
2. Run cells in order:
   - Load nuScenes mini
   - Build trajectory database
   - Define model + environment + reward
   - Train PPO
   - Generate evaluation plots + per-scene GIFs + per-scene reward curves

> Note: You must have nuScenes mini data available and the required Python packages installed (nuScenes devkit, Stable-Baselines3, gymnasium/gym, numpy, matplotlib, imageio, etc.)


## Project Highlights
- Designed an RL environment for trajectory tracking with realistic vehicle dynamics
- Built and justified reward shaping with stability-focused modifications
- Implemented multi-scene training and evaluation using normalization correctly
- Produced professor-friendly visual evidence: GIFs + reward curves + summary tables


## Author
**Samia Sahid**
