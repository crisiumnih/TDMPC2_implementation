### Beginning 
Data driven models are better as sample efficiency can be improved and scales with computation budget.

**Sample efficiency** - Model-based approaches generally require fewer interactions with the environment (samples) to learn effective control strategies compared to model-free methods.

But it is costly to plan over long horizons. 

This paper aims to combine strengths of model-based and model-free learning.

**Latent Model** - predicts how the system evolves over time, in latent space.
**Latent Space** - compressed, task-relevant representation of the state, learned from data, which captures the key features of the system necessary for solving the task.

### Gist 
Using an internal model, agent can plan trajectory ahead of time that leads to desired behaviour, while model free approaches rely on purely trial and error.

**Problem 1** - Can't plan ahead as expensive computationally 
**Problem 2** - An error in model can propagate in policy causing inaccurate roll-outs(simulated trajectories)

### TEMPORAL DIFFERENCE LEARNING FOR MODEL PREDICTIVE CONTROL (TD-MPC)

- Combines short-term trajectory optimization with long-term return estimation.
- MPC typically optimizes actions over a short time horizon based on model dynamics.
- Both a **latent dynamics model** and a **value function** are learned jointly using TD learning. This makes the framework more data-efficient and capable of handling long-term objectives.
- It performs **trajectory optimization** based on short-term reward estimates from the learned model and uses the **value function** to estimate the long-term return, ensuring that the system’s actions align with both immediate and future objectives

3 key changes - 
1. Removing unnecessary quantities (Latent Space)
2. Back-propagate gradients from rewards improving reward over long horizons
3. Modality-agnostic prediction loss in latent space enforces temporal consistency without explicit state prediction

**Modality-agnostic prediction loss** - method works independently of the type of input data
**~in latent space** - The model predicts how the latent representation evolves over time, rather than explicitly predicting the raw data
**temporal consistency** - If the latent representations are temporally consistent, they will accurately reflect the system’s evolution over time
**without explicit state prediction** - Predicting raw data can be computationally tough, instead using latent space, the model can focus on learning the underlying structure of the task without being worried in the complexities of raw data
**Note -** **loss** refers to a function that measures how well the model's predictions align with the desired outcomes or targets

#### Formulation

**MDP** - ($\mathcal{S},\mathcal{A},\mathcal{T},\mathcal{R},\gamma,p_0$) 
- $\mathcal{S}$ continuous state space, $\mathcal{A}$ continuous action space
- $\mathcal{T}$ is the transition function, function that maps a starting state, an action, and a resulting state to a non-negative real number
- $\mathcal{R}$ is a reward function
- $\gamma$ discount factor $\gamma \in [0,1)$
- $p_0$ initial state distribution

We aim to find a policy $$\Pi_\theta:\mathcal{S}\rightarrow\mathcal{A}$$
with parameters $\theta$
to maximise the reward which is formulated as - 
$$\mathbb{E}_{\Gamma\sim\Pi_\theta}\left[\sum_{t=1}^{\infty}\gamma^tr_t\right], r_t\sim\mathcal{R}(.|s_t,a_t)$$
How do we interpret this?
- A policy $\Pi$ with parameters $\theta$ which takes state $\mathcal{S}$ as input and maps to action $\mathcal{A}$ 
- $\mathbb{E}_{\Gamma\sim\Pi_\theta}$ Expected value of the function inside the brackets for a trajectory ($\Gamma$) following a policy $\Pi_\theta$ 
- $\left[\sum_{t=1}^{\infty}\gamma^tr_t\right]$ summing up the discounted rewards for time $t=1$ to $\infty$
- $r_t\sim\mathcal{R}(.|s_t,a_t)$ , tells us that $r_t$ is a reward function that gives reward for a time $t$ based on current state $s_t$ and action $a_t$

**Q-learning** 
- Goal of TD-learning is to estimate $Q^*$ (state value function) representing discounted return from any state-action pair $(s,a)$ 
- $Q_\theta(s,a)$ is a parametric approximation of $Q^*$ with parameters $\theta$
- Function approximates bellman equation $Q^*(s,a)=\max_{a'}\mathbb{E}[\mathcal{R}(s,a)+\gamma Q^* (s',a')]$ 
- Since $Q^*$ is unknown TD-learning iteratively updates $Q_\theta$ to make it closer to $Q^*$ 
- Update rule which adjusts $\theta$ minimizing the squared difference between $Q_\theta(s,a)$ and target Q y: $$\theta_{k+1}\leftarrow \arg\min_\theta\mathbb{E}_{(s,a,s')\sim\mathcal{B}}||Q_\theta(s,a)-y||_2^2$$
How do we interpret this?
- $\arg\min_\theta||Q_\theta(s,a)-y||_2^2$ finds value of $]theta$ that minimizes the error b/w $Q_\theta(s,a)$ and target Q y
- $(s,a,s')\sim\mathcal{B}$ indicates training samples are drawn from a replay buffer $\mathcal{B}$. Which stores previous states, action, next state. This buffer iteratively grows as new data is collected.

Now an interesting part is that target Q y is given as :$$y=\mathcal{R}(s,a)+\gamma\max_{a'}Q_{\theta-}(s',a')$$
- $\theta^-$ represents a slow-moving average of current parameters $\theta$, which updates according to; $$\theta_{k+1}^-\leftarrow(1-\zeta)\theta_k^-+\zeta\theta_k$$ where $\zeta \in [0,1)$

Why this slow moving $\theta^-$?

**MPC** - 
$\Pi$ represents method of finding optimal actions,
- In actor-critic RL it works for maximising a function Q
- In MPC, it is not parameterised instead it represents a trajectory optimisation procedure that estimates a sequence of actions over a finite horizon $a_{t:t+H}$
Actor-critic RL? -> The actor-critic algorithm is a type of reinforcement learning algorithm that combines aspects of both policy-based methods (Actor) and value-based methods (Critic).

- Unlike RL, which maximises over an infinite horizon, MPC optimizes a finite sequence of actions, aiming to **maximize cumulative rewards over that finite time** without discounting. $$\Pi_\theta^{MPC}(s_t)=\arg \max_{a_t:t+H}\mathbb{E}\left[\sum_{i=t}^H\gamma^i\mathcal{R}(s_i,a_i)\right]$$
- $\gamma$ is taken 1

This equation gives purely myopic solution and focuses on optimizing within the **finite horizon H**.

### Framework
