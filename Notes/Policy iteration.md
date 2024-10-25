We assume that environment evolves according to MDP$$P(s',s,a)=Pr(s_{k+1}=s'|s_k=s,a_k=a)$$The probability that my state is $s'$ is some probability with condition my current state $s$ and current action $a$
$$R(s',s,a)=Pr(r_{k+1}|s_{k+1}=s'|s_k=s,a_k=a)$$
Let's start with a Value function $$V_\pi(s)=\mathbb{E}\left(\sum_{k}\gamma^k r_k|s_0=s\right)$$
Adds up expected future reward given that you are in state s with optimal policy $\pi$
$$V(s)=\max_{\pi}\mathbb{E}\left(r_0+\sum_{k=1}^\infty\gamma^kr_k|s_1=s'\right)$$
Given the next time step is $s'$
$$V(s)=\max_\pi\mathbb{E}(r_0+\gamma V(s')) \qquad \text{Bellman's Equation}$$ So we can always extract a policy $\pi$ which maximises the above equation
$$\pi=\arg \max_\pi\mathbb{E}(r_0+\gamma V(s'))$$



#### Value Iteration
$$V(s)=\max_a\sum_{s'}P(s'|s,a)(R(s',s,a)+\gamma V(s'))$$Assumes I know the model to know where I'll be and assumes that I know the value of next state

- Start with arbitrary values V₀(s) for all states (often V₀(s) = 0)
- Set convergence threshold $\epsilon$
- Set discount factor γ
Stop when $\max|V_{k+1}(s)-V_{k}(s)|<\epsilon$
- Extract optimal policy

#### Policy iteration
- Start with arbitrary policy π₀
- Set convergence threshold ε
- Set discount factor γ
$$V(s)=\sum_{s'}P(s'|s,\pi(s))(R(s',s,\pi(s))+\gamma V(s'))$$
- policy improvement for each state s'

**PI typically converges in fewer iterations but each iteration is more computationally intensive**

**Quality function** - $Q(s,a)$ Quality of state/action pair
$$\displaylines{Q(s,a)=\mathbb{E}(R(s',s,a)+\gamma V(s'))\\
Q(s,a)=\sum_{s'}P(s'|s,a)(R(s',s,a)+\gamma V(s'))}$$
