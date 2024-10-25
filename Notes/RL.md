- learning what to do, map situations to actions to maximize reward
- Works on reward hypothesis
- Feedback is delayed
- Action Sequence matters
- Controlled -> set of rules defined for environment 
- can be generalized across tasks


#### Elements
- Policy -> Agent's way of learning 
- Model -> Representation of env
- History -> Sequence of observaations, actions and reqards
- reward signal -> goal of RL problem
- State -> function of history


#### Agent
##### Model 
- Predicts what env will do next
- contains transitions and rewards 



### K-Armed Bandit
- Exploitation 
- Exploration 
- epsilon greedy


$$Q_t(a)=\frac{\text{sum of reqrds when a taken prior to t}}{\text{number of times taken prior to t}} = \frac{\sum_{i-1}^{t-1}}{}$$


#### Describing env
- $$\mathbb{P}[S_{t+1}|S_t] =\mathbb{P}[S_{t+1}|S_1,\cdots S_t] $$



```tikz
\begin{document}
\begin{tikzpicture} 
\draw[step=1cm,black,very thin] (0,0) grid (5,5);
\end{tikzpicture}
\end{document}
```

