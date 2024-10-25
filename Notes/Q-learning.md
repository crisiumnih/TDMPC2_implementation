**Quality function** - $Q(s,a)$ Quality of state/action pair
$$\displaylines{Q(s,a)=\mathbb{E}(R(s',s,a)+\gamma V(s'))\\
Q(s,a)=\sum_{s'}P(s'|s,a)(R(s',s,a)+\gamma V(s'))}$$
When we do not have model of reward or P


### Monte Carlo Learning
Episodic learning method
$$R_{\sum}=\sum_{k=1}^n\gamma^kr_k\qquad \text{Total reward over episode}$$
- Must have definitive end
- $$V^{new}(s_k)=V^{old}(s_k)+\frac1n(R_\sum-V^{old}(s_k))$$
- $$Q^{new}(s_k)=Q^{old}(s_k)+\frac1n(R_\sum-Q^{old}(s_k))$$
- Has no bias

### TD learning: TD(0)
- Preference to recent reward actions
$$V(s_k)=\mathbb{E}(r_k+\gamma V(s_{k+1}))$$
$$V^{new}(s_k)=V^{old}(s_k)+\alpha\left(r_k+\gamma V^{old}(s_{k+1})-V^{old}(s_{k})\right)$$
TD error -> $(r_k+\gamma V^{old}(s_{k+1})-V^{old}(s_{k})$
TD target estimate $R_\sum$ -> $(r_k+\gamma V^{old}(s_{k+1})$


##### TD(N)
$$V(s_k)=\mathbb{E}(r_k+\gamma V(s_{k+1}))\Rightarrow \mathbb{E}(r_k+\gamma r_{k+1}+ \gamma^2V(s_{k+2}))$$
- now taking 2 steps in future
- let us expand to n step cumulative reward
$$R_\sum^n=\sum_{j=0}^n\gamma^jr_{k+j} + \gamma^{n+1}V(s_{k+n+1})$$
- n->$\infty$ it converges to monte carlo

Q-learning is just TD learning on TD-Q
##### TD-$\lambda$
$$R_\sum^\lambda=(1-\lambda)\sum_{k=1}^\infty\lambda^{n-1} R_\sum^n$$
- weighing recent stuff more or older stuff more

### Q-Learning

$$Q^{new}(s_k,a_k)=Q^{old}(s_k,a_k)+\alpha\left(r_k+\gamma \max_aQ(s_{k+1},a)-Q^{old}(s_{k},a_k)\right)$$
- If i got more reward than i expected i should increase my Q function at that s and that $a_k$
- We are maximising over $a_k$
- It doesn't have to be optimal
- I can take sub-optimal $a$ but i need to maximise the Q (OFF POLICY TD(0))


### SARSA - State-Action-Reward-State-Action
- you are in a $s_k$ and take $a_k$ get reward and take another $a_{k+1}$ in $s_{k+1}$
- $$Q^{new}(s_k,a_k)=Q^{old}(s_k,a_k)+\alpha\left(r_k+\gamma Q^{old}(s_{k+1},a_{k+1})-Q^{old}(s_{k},a_k)\right)$$
- For this optimal policy is required to work and not degrade
- Works for TD-(N)
- ON POLICY TD Learning

Q-learning -> better/faster learning, higher variance
SARSA -> Safer, better total reward, while learning