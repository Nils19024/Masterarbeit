# Master's Thesis — Universal Base Control for Mobile Manipulation

Student: Nils Schmidt

## Research objective

Learn a morphology-agnostic policy that predicts base control given the end effectors' desired trajectories. The main idea has already been studied and proven effective when trained on separate robots [1]. However, with robot manufacturing booming in recent years, it would be valuable to have a base policy that works zero-shot or requires minimal finetuning. The core reasoning is that the general behavior the policy has to learn (driving the base towards the end effector goal and trajectory while avoiding obstacles) is common across robots. What differs is only what each robot can actually reach given its specific kinematic chain (this is the hardest part to learn) and the base shape and size for collision.

![Research concept showing robot morphologies, the morphology encoder, the base policy, and execution](Grafik%20-%20Universelle%20Basissteuerung.png)

## Approach

- Encode the robot's kinematic chain and use it as additional conditioning for the policy. We might pre-train this encoder on a dummy task, e.g. predicting the forward kinematics of each joint, which is cheap to obtain and parallelize.
- Use modified versions of existing robot URDFs, as done in [2, 3], to prevent overfitting to the training robot descriptions.
- Train without simulation, using only forward and inverse kinematics, to speed up training. Testing and evaluation will then be done in simulation. We might also finetune in simulation to check for self collision.

## Thesis scope

- Output: a trained base drive policy conditioned on end effector trajectories.
- Evaluation: train on a large set of robot morphologies and evaluate on an unseen subset. Use a rotating set of held-out robots to really verify that the method generalizes to unseen robots.

## Work plan

[Open the interactive work plan](https://github.com/Nils19024/Masterarbeit/issues/1) to check off completed tasks and track progress.

### Week 0: Preliminaries and background

- Read the provided related work.
- Understand how FK and IK work.
- Understand URDFs.

### Week 1–2: Simulation setup

- Choose a simulator (probably MuJoCo, but we can explore other options like Isaac).
- Collect a set of mobile manipulators (single-arm like Franka Omron and Tiago, or bimanual like Agibot G2) and load them in the simulator.
- If possible, already define base drive controllers: (vx, vy, ω) velocity commands for omni drives and (vx, ω) for diff drives.
- If time allows, explore FK/IK solvers (Pink, Mink, Pinocchio) and integrate them with the simulator.

### Week 3–4: Kinematic diversity

- Explore how to modify the robots' kinematic chains to diversify the training set [2, 3].
- Pretrain a model (transformer or graph transformer) to encode the kinematic chain and predict link poses.

### Week 5–6: Obstacle-free setting

- Define the rewards.
- Train a policy with RL to drive the base (i.e. predict base velocities).
- Start with one robot at a time as a baseline.

### Week 7–8: Universal policy

- Train the multi-robot policy.

### Week 9–10: Evaluation tasks

- Define evaluation tasks and metrics. Some tasks have to be defined based on the kinematic feasibility of each robot.
- Evaluate on held-out sets.

### Week 11–14: Obstacle avoidance

- Define how obstacles are randomly generated.
- Add lidars (2D and 3D) to get occupancy maps.
- Generate feasible random trajectories.
- Define a collision penalty.
- Use the pretrained policies from previous steps as starting policies (curriculum learning).

### Week 15–18: Training and evaluation with obstacles

- Train new policies.
- Evaluate them.

### Week 19–21: Experiments

- Additional experiments and ablations.
- If possible, add baselines.

### Week 22–24: Thesis

- Thesis writing and presentation.

## Supervision and working practices

1. The project is yours; I am here to supervise and help. Take ownership: care about it, propose and explore ideas, and don't take a passive seat where you only do as told. It's normal to be unsure at first, which is why I provide guidance and some predefined milestones. These are only guidelines, and eventually you will find your own way and define your own goals. When stuck, don't wait idly for the next meeting: try to debug and come up with ideas for how to debug.
2. Take the time to understand each step. There is no need to rush results we don't understand. I would rather you fully understand the concepts than show me good-looking results you cannot explain.
3. You can use LLMs to help with code, but don't let them take over. You should always be in control of the codebase, line by line.
4. Keep your codebase modular, minimal and clean, and use abstract classes whenever possible. It's fine if it gets messy while developing and testing; just take some time to refactor and clean up after each major step.
5. Holidays, sick days and skipping meetings are your right; don't feel pressured. Just ask to skip a meeting, which also applies if you feel you don't have much progress to discuss.
6. Visualizations help both of us understand and debug. Prepare some slides or a weekly update so we can follow progress, and try to visualize every step of the process rather than relying only on numbers.
7. Use git and uv to set up your workspace. Every time you add a new feature to test, create a branch, test there, then merge back into main.
8. Always use Hydra and load things from config. This lets you easily switch hyperparameters and test different things.

## Related work

### Most relevant

- N2M2: [https://mobile-rl.cs.uni-freiburg.de/](https://mobile-rl.cs.uni-freiburg.de/)
- One Policy to Control Them All: [https://arxiv.org/pdf/2007.04976](https://arxiv.org/pdf/2007.04976)
- Towards Embodiment Scaling Laws in Robot Locomotion

### Further reading

- ReLMoGen: Integrating Motion Generation in Reinforcement Learning for Mobile Manipulation
- XMoP: Whole-Body Control Policy for Zero-shot Cross-Embodiment Neural Motion Planning
- CrossFormer: [https://crossformer-model.github.io/](https://crossformer-model.github.io/)
