# PyMOOPS: Reinforcement Learning-Based Simulation Environment for Multi-Objective Optimization in Power Scheduling

## Project Description
### Objective 
**Cost functions**: $\cal C_{it}=z_{it}f^c(p_{it})+z_{it}(1-z_{i,t-1})s_{it}^{ON,c}+(1-z_{it})z_{i,t-1}s_{it}^{OFF,c}$ where $f^c(p_{it})=\alpha_i^cp_{it}^2+\beta^cp_{it}+\delta+|\rho^csin[\varphi^c_i(p_{it}^{min}+p_{it})]|$

**Emission functions**: $\cal E_{it}=z_{it}f^e(p_{it})+z_{it}(1-z_{i,t-1})s_{it}^{ON,e}+(1-z_{it})z_{i,t-1}s_{it}^{OFF,e}$ where $f^e(p_{it})=\alpha_i^ep_{it}^2+\beta^ep_{it}+\delta+\rho^eexp(\varphi^e_ip_{it})$

The MOPS problem:
$$\cal \Phi(C,E)=\sum\limits_{t=1}^{24}\sum\limits_{i=1}^n[\omega_0C_{it}+\sum\limits_{h=1}^m\omega_h\eta_{ih}E_{it}^{(h)}]$$

where $\displaystyle \eta_i = exp{\frac{\nabla f^c(p_i)/\nabla f^e(p_i)}{{max[\nabla f^c(p_i)/\nabla f^e(p_i);\forall i]-min[\nabla f^c(p_i)/\nabla f^e(p_i);\forall i]}}}\forall i$

subject to:

| Constraints | Specification | 
| --------------- | --------------- | 
| Min and max power capacity limits:    | $\cal z_{it}p_{i*}^{min}\le p_{it}\le z_{it}p_{i*}^{max}$ | 
| Max ramp down and up rates:    | $\cal z_{it}p_{i,t-1}-z_{it}p_{it}\le p_{i*}^{down}, z_{it}p_{it}-z_{i,t-1}p_{it}\le p_{i*}^{up}$    | 
| Min operating (online/offline) durations:     | ${\cal t}t_{it}^{ON}\ge tt_{i*}^{OFF}, tt_{it}^{OFF}\ge tt_{i*}^{OFF}$    | 
| Power supply and demand balance:    | $\cal \sum\limits_{i=1}^nz_{it}p_{it}=d_t$   | 
| Min available reserve capacity:   | $\cal \sum\limits_{i=1}^nz_{it}p_{it}^{max}\ge (1+ r) d_t$    | 

Within the framework of MARL, the generating units are represented as multi-RL agents, each with different unit-specific characteristics and multiple conflicting objectives. The framework MARL manifests the form of state $\cal S$, action  $\cal A$, transition (probability) function  $\cal P$ and reward  $\cal R$ for a sequence of discrete timesteps $t$. 
### The MARL Framework
The scheduling horizon is an hourly divided day, each hour of a day is considered a timestep $t,\forall t$.  Hence, one cycle of determination of commitments and load dispatches for a day represents a complete episode.
- **State Space** $\cal s_t\in S$: The state $\cal s_t$ at timestep $t$ consists of the current timestep$t$, a vector of minimum capacities $p_t^{min}$, a vector of maximum capacities $p_t^{max}$, a vector of current (online/offline) duration $tt_t$ ; and the demand $d_t$.
- **Action Space** $\cal a_t\in A$: The action $\cal a_t$ consists of the decision of either of the two possible actions (switch-to/stay ON (1) or switch-to/stay OFF (0)) of each of the $n$ agents at each timestep $t$ (or state $\cal s_t$).
- **Transition Function** $\cal P(s_t' |s_t,a_t)$: Once the agents take actions $\cal a_t\in A$ in $\cal s_t\in S$, the transition (or probability) function $\cal P(s_t' |s_t,a_t)$ determines whether to advance to the next succeeding state $\cal s_t'=s_{t+1}$ or re-initialize the next state $\cal s_t'=s_{0}$.
  - The illegal decisions of the agents are corrected by the environment.
  - The environment adjusts for both excess and shortages of power supplies.
- **Reward function** $\cal R$: The agents get a common reward based on a predefined reinforcement function $\cal R(s_t,a_t,s_t')$ based on the action $\cal a_t\in A$ in state $\cal s_t\in S$.

The MOPS dynamics can be formally formulated as a 4-tuple $\cal (S,A,P,R)$ Markov Decision Process (MDP), which can then be used to train a custom deep learning model.The practical viability of the environment is evaluated on different test systems featuring mono- to tri-objective problems.





