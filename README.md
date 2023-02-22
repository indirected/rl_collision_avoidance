This is the training code for:

**PA-CADRL (new):** Mohammad Bahrami Karkevandi, Samaneh Hosseini Semnani, "Multi-Agent Collision Avoidance with Provident Agents using Deep-Reinforcement Learning"[preprint]

In this paper/repo we add providence to the agents by using their relative velocities (in a sense) and detect which agents are moving aggressively toward each other to penalize them. This repo adds this capability to the original code, nothing is removed. Additionally, the rewarding mechanism has been improved to favor penalizing collisions prior to rewarding goal achievements.

Please note that this repo and the [gym environment repo](https://github.com/indirected/gym-collision-avoidance) are both a fork of previous work of a different author. please make sure to cite them as well when required.

The repo contains the source code and configuration used to train PA-CADRL policy introduced in the paper above.

Our work is based on the previous work mentioned below:

**Journal Version (previous work);** M. Everett, Y. Chen, and J. P. How, "Collision Avoidance in Pedestrian-Rich Environments with Deep Reinforcement Learning", in review, [Link to Paper](https://arxiv.org/abs/1910.11689)

**Conference Version (previous work):** M. Everett, Y. Chen, and J. P. How, "Motion Planning Among Dynamic, Decision-Making Agents with Deep Reinforcement Learning", IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS), 2018. [Link to Paper](https://arxiv.org/abs/1805.01956), [Link to Video](https://www.youtube.com/watch?v=XHoXkWLhwYQ)

The [gym environment code](https://github.com/indirected/gym-collision-avoidance) is included as a submodule.

### Install

Grab the code from github, initialize submodules, install dependencies and src code
```bash
# Clone either through SSH or HTTPS
git clone --recursive https://github.com/indirected/rl_collision_avoidance.git

cd rl_collision_avoidance
./install.sh
```

There are some moderately large (10s of MB) checkpoint files containing network weights that are stored in this repo as Git LFS files. They should automatically be downloaded during the install script. 

### Train RL (starting with a network initialized through supervised learning on CADRL decisions)

To start a PA-CADRL training run:
```bash
./train.sh PA_TrainPhase1
```

<!-- It should produce an output stream somewhat like this:
<img src="docs/_static/terminal_train_phase_1.gif" alt="Example output of terminal">
 -->

To load that checkpoint and continue phase 1 or switch to phase 2 of training, update the `LOAD_FROM_WANDB_RUN_ID` path in `Config.py` and do any of the below:
```bash
./train.sh PA_TrainPhase1_CONT
OR
./train.sh PA_TrainPhase2
```

By default, the RL checkpoints will be stored in `RL_tmp` and I think files will get overwritten if you train multiple runs. Instead, I like using `wandb` as a way of recording experiments/saving network parameters. To enable this, set the `self.USE_WANDB` flag to be `True` in `Config.py`, then checkpoints will be stored in `RL/wandb/run-<datetime>-<id>`.

### To run experiments on AWS
Start a bunch (e.g., 5) of AWS instances -- I used `c5.2xlarge` because they have 8vCPUs and 16GB RAM (somewhat like my desktop?). Note: this is just an example, won't work out of box for you (has hard-coded paths)

Add the IP addresses into `ga3c_cadrl_aws.sh`.
```bash
./ga3c_cadrl_aws.sh panes
# C-a :setw synchronize-panes -- will let you enter the same command in each instance
```

Then you can follow the install & train instructions just like normal. When training, it will prompt you for a wandb login (can paste in the authorization code from app.wandb.ai/authorize).

### Observed Issues
If on OSX, when running the `./train.sh` script, you see:
```bash
objc[39391]: +[__NSCFConstantString initialize] may have been in progress in another thread when fork() was called.
objc[39391]: +[__NSCFConstantString initialize] may have been in progress in another thread when fork() was called. We cannot safely call it or ignore it in the fork() child process. Crashing instead. Set a breakpoint on objc_initializeAfterForkError to debug.
```
just add this ENV_VAR: `export OBJC_DISABLE_INITIALIZE_FORK_SAFETY=YES`.

### If you find this code useful, please consider citing:

```
@inproceedings{Everett18_IROS,
  address = {Madrid, Spain},
  author = {Everett, Michael and Chen, Yu Fan and How, Jonathan P.},
  booktitle = {IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS)},
  date-modified = {2018-10-03 06:18:08 -0400},
  month = sep,
  title = {Motion Planning Among Dynamic, Decision-Making Agents with Deep Reinforcement Learning},
  year = {2018},
  url = {https://arxiv.org/pdf/1805.01956.pdf},
  bdsk-url-1 = {https://arxiv.org/pdf/1805.01956.pdf}
}
```