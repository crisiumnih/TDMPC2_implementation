$$\displaylines{\text{Consider a linear system} \\
x_{k+1}=Ax_k+Bu_k \\
z_k=C_k \\ 
\\
z_k \text{ is set of state variables that should follow a desrired trajectory. } \\ 
\text{Assuming full state feedback,}
\\
\text{Length of predicted horizon is denoted by }f \\
\text{Length of control horizon is denoted by }v \\ \\
u_{k+i∣k} \text{​ represents the control input at a future time step k+i,}\\ \text{where k is the current time step.} \\
\text{The subscript i indicates how many steps into the future we are looking.}
\\
\\
x_{k+1|k}\text{ is the prediction of state vector at time instant k+i and made at k}\\
z_{k+1|k}\text{ is the prediction of output vector at time instant k+i and made at k} \\ \\
\textbf{The goal of the model predictive controller is to determine a set of control inputs } \bf{u_{k+i|k}} \\ 
\textbf{at the time instant k, that will make the output of the system } \bf{z_{k+i|i}} \\
\textbf{to follow a desired control trajectory over the prediction horizon.} \\
\textbf{This is done by using the knowledge of the current state } \mathbf{x}_{k}, \\\textbf{ and the system model matrices A,B, and C to predict the output trajectory }z_{k+i|k}, \\ 
\textbf{and then minimize the difference between desired and predicted trajectories. }}$$


$$z_{k+v|k}=CA^vx_k+\begin{bmatrix}CA^{v-1}B & CA^{v-2}B & \cdots & CAB & CB\end{bmatrix}\begin{bmatrix}u_{k|k} \\ u_{k+1|k} \\ \vdots \\ u_{k+v-1|k} \end{bmatrix}$$

$$\begin{bmatrix}z_{k+1|k}\\\vdots\\z_{k+v|k} \\z_{k+v+1|k} \\ \vdots \\ z_{k+f|k} \end{bmatrix}=\begin{bmatrix}CA \\ CA^2 \\ \vdots\\ CA^v \\ CA^{v+1}\\ \vdots \\ CA^f \end{bmatrix}x_k+\begin{bmatrix}CB & 0  & \cdots & 0 & 0\\\vdots & \vdots & \vdots & \vdots& \vdots \\ CA^{v-1}B & CA^{v-2}B & \cdots & CAB & CB\\ \vdots & \vdots & \vdots & \vdots& \vdots \\ CA^{f}B & CA^{f-1}B & \cdots & CA^{f-v+1}B & CA_{f,v}B\end{bmatrix}\begin{bmatrix}u_{k|k} \\ u_{k+1|k} \\ \vdots \\ u_{k+v-1|k} \end{bmatrix}$$


$$z=Ox_k+Mu$$
- Let the desired output be represented with superscript 'd'

$$z^d=\begin{bmatrix}z^d_{k+1}\\ z^d_{k+2} \\ \vdots \\z^d_{k+f}\end{bmatrix}$$
- In MPC the problem is to determine the control input such that cost function is minimized 
	- $min||z^d-z||=min(z^d-z)^T(z^d-z)$
- we do not have control on the magnitude of control inputs in this case
- **Furthermore, we need to introduce weights in the cost function to have better control of the convergence of the algorithm**

### Cost function
##### Penalising inputs
$$\displaylines{J_{\mathbf{u}}=\mathbf{u}_{k|k}^{T}Q_{0}\mathbf{u}_{k|k}+(\mathbf{u}_{k+1|k}-\mathbf{u}_{k|k})^{T}Q_{1}(\mathbf{u}_{k+1|k}-\mathbf{u}_{k|k})+\\ +(\mathbf{u}_{k+2|k}-\mathbf{u}_{k+1|k})^{T}Q_{2}(\mathbf{u}_{k+2|k}-\mathbf{u}_{k+1|k})+(\mathbf{u}_{k+3|k}-\mathbf{u}_{k+2|k})^{T}Q_{3}(\mathbf{u}_{k+3|k}-\mathbf{u}_{k+2|k}) + \\+  \ldots  + (\mathbf{u}_{k+v-1|k}-\mathbf{u}_{k+v-2|k})^{T}Q_{v-1}(\mathbf{u}_{k+v-1|k}-\mathbf{u}_{k+v-2|k}}$$
- Where $Q_i$ is symmetric matrix selected by user. Aim is to penalise control input $u_{k|k}$ and difference between consequent control inputs.

$$\begin{bmatrix}I & 0 & 0 & 0 & \ldots &  0 \\ -I & I & 0 & 0 & \ldots &  0 \\ 0 & -I & I & 0 & \ldots &  0 \\ \vdots & \vdots & \vdots & \vdots & \ldots & \vdots  \\ 0 & 0 & \ldots & 0 & -I &  I  \end{bmatrix}\begin{bmatrix} \mathbf{u}_{k|k} \\ \mathbf{u}_{k+1|k} \\ \mathbf{u}_{k+2|k} \\  \vdots \\ \mathbf{u}_{k+v-2|k}  \\ \mathbf{u}_{k+v-1|k}  \end{bmatrix} =\begin{bmatrix} \mathbf{u}_{k|k}  \\ \mathbf{u}_{k+1|k}- \mathbf{u}_{k|k} \\ \mathbf{u}_{k+2|k}- \mathbf{u}_{k+1|k} \\ \vdots \\  \mathbf{u}_{k+v-1|k}- \mathbf{u}_{k+v-2|k}  \end{bmatrix}$$
-> $W_1u$
- Now $J_u$ is 
	- $$J_u=({W}_{1} {\mathbf{u}} \big)^{T} {W}_{2}\big( {W}_{1} {\mathbf{u}} \big)=\big( {\mathbf{u}}^{T} {W}_{1}^{T} \big) {W}_{2}\big( {W}_{1} {\mathbf{u}} \big)= {\mathbf{u}}^{T} {W}_{1}^{T}  {W}_{2} {W}_{1} {\mathbf{u}} = {\mathbf{u}}^{T} {W}_{3} {\mathbf{u}}$$
	- Where $W_3=W_1^TW_2W_1$
	- $W_2=\begin{bmatrix}Q_0 & 0 &0 & \cdots & 0 \\ 0 & Q_1 & 0 & \cdots & 0 \\ & & \ddots & & \\ 0 & 0 & 0 & \cdots & Q_{v-1}\end{bmatrix}$

##### Penalising tracking error
$$\displaylines{J_{\mathbf{z}} & =\big( \mathbf{z}_{k+1}^{d} -\mathbf{z}_{k+1|k}\big)^{T}P_{1}\big( \mathbf{z}_{k+1}^{d} -\mathbf{z}_{k+1|k}\big)+\big( \mathbf{z}_{k+2}^{d} -\mathbf{z}_{k+2|k}\big)^{T}P_{2}\big( \mathbf{z}_{k+2}^{d} -\mathbf{z}_{k+2|k}\big) \\& +\ldots+\big( \mathbf{z}_{k+f}^{d} -\mathbf{z}_{k+f|k}\big)^{T}P_{f}\big( \mathbf{z}_{k+f}^{d} -\mathbf{z}_{k+f|k}\big)}$$

Now by introducing $W_4=\begin{bmatrix}P_1 & 0 &0 & \cdots & 0 \\ 0 & P_2 & 0 & \cdots & 0 \\ & & \ddots & & \\ 0 & 0 & 0 & \cdots & P_{f}\end{bmatrix}$

$$\displaylines{J_z=(z^d-z)^TW_4(z^d-z) \\
=(z^d-Ox_k-Mu)^TW_4(z^d-Ox_k-Mu) \\
=(s-Mu)^TW_4(s-Mu)}$$



**Now summing up the cost we obtain**
$$\displaylines{J  = (  {\mathbf{s}}-M  {\mathbf{u}} )^{T}  {W}_{4}(  {\mathbf{s}}-M  {\mathbf{u}})+  {\mathbf{u}}^{T}  {W}_{3}  {\mathbf{u}} \\J =   {\mathbf{s}}^{T}  {W}_{4}  {\mathbf{s}}-  {\mathbf{s}}^{T}  {W}_{4}M  {\mathbf{u}}-  {\mathbf{u}}^{T}M^{T}  {W}_{4}  {\mathbf{s}}+  {\mathbf{u}}^{T}M^{T}  {W}_{4}M  {\mathbf{u}}+  {\mathbf{u}}^{T}  {W}_{3}  {\mathbf{u}}}$$

- To miminize this cost wrt u 
- $\frac{\partial J}{\partial u}=0$

- We obtain $$(M^TW_4M+W_3)u=M^TW_4s$$
- For u, $$u = (M^TW_4M+W_3)^{-1}M^TW_4s$$


```python
# Solve MPC optimization problem
def solve_mpc(theta_ref, theta, dtheta, tau_ini, l, k, m, g, dt, Q11, Q22, R, N, tau_max, delta_tau_max):

    # Linear constraints on the rate of change of tau: -delta_tau_max <= tau[i+1] - tau[i] <= delta_tau_max for all i in the N - 1
    # Implemented using LinearConstraint as -delta_tau_max <= delta_tau_matrix * tau <= delta_tau_max
    delta_tau_matrix = np.eye(N) - np.eye(N, k=1)
    constraint1 = LinearConstraint(delta_tau_matrix, -delta_tau_max, delta_tau_max)

    # We need a constraint on the rate of change of tau[0] respect to its previous value, which is tau_ini[0]
    first_element_matrix = np.zeros([N, N])
    first_element_matrix[0, 0] = 1
    constraint2 = LinearConstraint(first_element_matrix, tau_ini[0]-delta_tau_max, tau_ini[0]+delta_tau_max)
    
    # Add constraints
    delta_tau_constraint = [constraint1, constraint2]

    # Bounds --> -tau_max <= tau[idx] <= tau_max for idx = 0 to N-1
    bounds = [(-tau_max, tau_max) for idx in range(N)]

    # Starting optimisation point for theta and dtheta are the current measurements
    theta0 = theta
    dtheta0 = dtheta

    # Minimization
    result = minimize(mpc_cost, tau_ini, args=(tau_ini, theta_ref, theta0, dtheta0, l, k, m, g, dt, Q11, Q22, R, N), bounds=bounds, constraints=delta_tau_constraint)

    # Extract the optimal control sequence
    tau_mpc = result.x

    return tau_mpc
```