# Legged Locomotion RL

Repository for learning about and implementing algorithms for legged (particularly bipedal) locomotion using reinforcement learning. 

## To-Do: RL Walking Replication
There's a ton of details in the legged_gym repo that we probably want to keep, like command and terrain curriculums. We can integrate domain randomization into this with multiple game-epochs (so multiple iterations of the entire training process with different randomizations). Just combining these two papers would be really cool.

### Terrain Definition
 - We want to use a flat plane for phase 1 training, so let's start with just that.
   - set mesh_type to plane in legged_gym/envs/base/legged_robot_config/Legged_robotCfg/terrain
### Rewards
 - Need to implement the following in legged_gym/envs/base/legged_robot.py
   - Motion Tracking
     - Need to draw on trajectory optimizations.
   - Task Completion
     - I think this is just if the robot is able to walk to the desired end point. This should be handled inside the legged_gym repo already. Need to figure this out better.
   - Smoothing
     - Need to draw on action history.
 - Should also look and see if the ones already implemented work well with ours, could use some of them (since this takes 4 mins to train for flat terrain, we could run a reward function optimizer).
### Controls
 - Cassie already has PD built in. But it needs the LPF. This should probably be implemented in rsl_rl/algorithms/ppo/act and just filter it before returning the action.
 - Action/Outcome history needs to be implemented into legged_gym/envs/legged_robot/_init_buffers as attributes. Should put trajectory optimizations inside there too and just have a boot-up loading util function.
 - Need to be able to set speed controls: just restrict to x movement for a straight line and make speed ranges very restrictive in commands/ranges in the legged config file
 - Need to also input the trajectory optimizations, short I/O, and long I/O latent representation into the PPO 'act'
   - Save this information to infos returned by env.step
   - Integrate CNN for long-history later, just use short-history for now. When we do, build it into the same prob module as the PPO.
   - Should just augment the observation (and critic observation) space with this info: rsl_rl/runners/on_policy_runner/OnPolicyRunner/learn.
### Training Phase Differences
 - **Phase 2**: Need to induce randomized speed controls within a range.
   - Can handle this in legged_gym/envs/base/legged_robot_config/LeggedRobotCfg/commands/ranges - change ranges to modify the linear velocity goals I think.
 - **Phase 3**: Need to induce:
   - Randomized Dynamics
     - legged_gym/envs/base/legged_robot_config/LeggedRobotCfg/domain_rand
     - Has friction, base mass randomization
   - Randomized Perturbations
     - legged_gym/envs/base/legged_robot_config/LeggedRobotCfg/domain_rand
     - Has push interval and velocity parameters
   - Randomized Terrain
 - For reference, in Phase 3, they use this figure, which says the non-dynamics randomization is 'optional' and they have an extremely extensive list of dynamics that they mess with:
<img width="525" alt="Screen Shot 2024-06-25 at 12 34 00 PM" src="https://github.com/wdc3iii/LeggedLocomotionRL/assets/10200978/387a856f-a55e-4b21-9f07-bc79ada5f1cd">
