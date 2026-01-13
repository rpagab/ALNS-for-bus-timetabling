# Optimisation of Bus Timetables: An Adaptive Large Neighbourhood Search-based Matheuristic with a Novel Operator Weight

Authors:
Robin Gaborit<sup>a</sup>, Evelien van der Hurk<sup>a</sup>, Otto Anker Nielsen<sup>a</sup>, Yu Jiang<sup>a, b, 1</sup>

<sup>a</sup>Department of Technology, Management and Economics, Technical University of Denmark, Denmark  
<sup>b</sup>Lancaster University Management School, Lancaster University, United Kingdom  
<sup>1</sup>Corresponding author

# Description

We developed a metaheuristic solution method to solve the model proposed by Lee et al. (2022). The model is an acyclic bus timetabling problem with time-dependent travel time and demand data. The solution method is an Adaptive Large Neighbourhood Search matheuristic accounting for computation times in operator selection. 

# Input data

The study focuses on three bus lines operating within the metropolitan area of Copenhagen, Denmark. These lines, designated 1A, 2A, and 3A, represented some of the most prominent bus routes in the city. We employ a dataset comprising the Danish Rejsekort smart card transactions from 2014.

A total of nine instances were produced based on this network. Each instance is named by one letter S, M or L and one number 1, 2 or 3. The letter indicates if the instance size is Small, Medium or Large. Files related to input data are in folder data. Durations are in minutes and times are in number of minutes after 7 a.m..

Folder data includes files config_S, config_M, and config_L that contains parameters common to all instances of the same size:
- hmin: minimum headway for each bus route
- hmax: maximum headway for each bus route
- dwellmin: minimum dwell time for each bus route and stop
- dwellmax: maximum dwell time for each bus route and stop
- transfermax: maximum transfer time per passenger group
- period: period duration
- horizon: maximum time span of the timetable
- nbuses: number of bus runs in each bus route
- weight_invehicle: coefficient of in-vehicle time in the objective function
- weight_wait: coefficient of initial waiting time in the objective function
- weight_transfer: coefficient of transfer time in the objective function
- weight_latearrival: coefficient of late arrival time in the objective function
- weight_earlyarrival: coefficient of early arrival time in the objective function
- exp_arrivalbuffer: half of the length of the expected arrival time interval

Folder data also includes one subfolder per instance. Each subfolder contains files for instance specific parameters:
- alpha: $\alpha_{g1}$ $\alpha_{g2}$ ...
- expArrival: center of expected arrival time interval of each passenger group
- groups (one line per group): $r_{g1}^{(1)}$ $r_{g1}^{(2)}$ ... ; $\Psi_{g1}^{(1)}$, $\Psi_{g1}^{(2)}$ ... ; $s_{g1}^{(0)}$ ; $s_{g1}^{(|\Psi_{g1}|)}$
- omega: (one line per transfer opportunity $\psi = (r1, r2, s)$): $r1$ $r2$ $s$ $w_{\psi}$
- period_horizon (repeats the corresponding config_ file): period horizon
- routes: $r1$ $r2$ ...
- stops: $s1$ $s2$ ...
- transfers (one line per transfer opportunity $\psi = (r1, r2, s)$): $r1$ $r2$ $s$
- tt_schedule (one line per bus route): $t_{r1s^{(1)}}^1$ $t_{r1s^{(1)}}^2$ ... ; $t_{r1s^{(2)}}^1$ $t_{r1s^{(2)}}^2$ ... ; ...

# Solution method parameters

The following parameters are used by the exact solution methods:
- maxCompTime: the maximum computation time, in seconds
- nbThreads: the number of threads used by Gurobi (eight for all experiments)
- initialSolution: "ini" ("noIni") indicated a random feasible initial solution is (not) provided to Gurobi
- validInequalities: 0 indicates the valid inequalities are not included; 1 indicates they are included as regular constraints; 2 indicates they are included as user cuts in a branch-and-cut

The following parameters are common to all experiments using our ALNS solution method:
- maxComptime = 7200: the maximum computation time, in seconds
- nbThreads = 8: the number of threads used by Gurobi in the MILP operator
- lmax = 0.1: the maximum reaction factor $\eta_{max}$
- sigmas = 1_0.005_1e-8: the rewards $\sigma_1$ _ $\sigma_2$ _ $\sigma_3$
- tinit = 100: the initial temperature $T_{ini}$
- tfin = 100: the final temperature $T_{fin}$
- validInequalities = 0: 0 indicates the valid inequalities are not included in the MILP operator

The following parameters differ between experiments using our ALNS solution method:
- operatorSet: 11 indicates both the MILP and the heuristic operator are used; 01 indicates the heuristic operator is removed; 10 indicated the MILP operator is removed
- exponentForTime: the exponent for time in the formula of the revised weights (two for the inverse-square rule)

# Implementation

The future version will include the Julia source code.

# Results

Folder Results contains four subfolders: baseALNS, exactMethods, impactOperators, and mechanismWeightUpdate. Durations are in minutes and times are in number of minutes after 7 a.m..

## baseALNS

Subfolder baseALNS includes subfolders Objective, PerIteration, and Timetable. In Objective, each file contains the objective function value produced by one run of the ALNS for one instance and the base parameters (full operator set and inverse-square rule). The name of an objective file is in the format Obj_instance_maxCompTime_nbThreads_operatorSet_lmax_sigmas_tinit_tfin_exponentForTime_validInequalities_run.txt. Besides other results, these results are used to produce Tables 2, 4, and 5.

In PerIteration, each file contains various variable values at each iteration of the ALNS. The useful columns are:
- time: elapsed time, in seconds
- best obj: objective function value of the best solution
- duration 1: total time consumed by the iterations employing the heuristic operator, in seconds
- duration 5: total time consumed by the iterations employing with the MILP operator, in seconds
These results are used to produce Figure 2.

In Timetable, each file contains the timetable (arrival and departure times of each bus route and run at each stop) of one ALNS run. The name of a timetable file is in the format Tim_instance_maxCompTime_nbThreads_operatorSet_lmax_sigmas_tinit_tfin_exponentForTime_validInequalities_run.txt.

## exactMethods

The six subfolders contains the results with the six exact methods (same notations as in Section 5.2.2). Each subfolder contains three subforlders. In Objective, the name of an objective file is in the format Obj_instance_maxCompTime_nbThreads_initialSolution_validInequalities_run.txt. These results are used to produce Tables 2 in Section 5.2.2. In Timetable, the name of a timetable file is in the format Tim_instance_maxCompTime_nbThreads_initialSolution_validInequalities_run.txt. If no feasible solution is produced, the timetable file is empty. In MILPSolution, each file contains all variables of the best solution found by Gurobi. The following notations are used:

| Model formulation | Solution file |
|----------|----------|
| $a_{rs}^k$        | a[$r, s, k$]        |
| $u_{rs}^k$        | u[$r, s, k$]        | 
| $d_{rs}^k$        | d[$r, s, k$]        |  
| $\varphi_{rs}^{k\tau}$        | phi[$r, s, k, \tau$]        |
| $\gamma_g^k$        | gamma0[$k, g$]        | 
| $\gamma_{\psi}^{k, k'}$        | gamma[$\psi, k, k'$]        | 
| $\delta_{g\psi_g^{(i)}}^{k, k'}$        | delta[$g, \psi_g^{(i)}, k, k'$]        | 
| $p_{g\psi_g^{(i)}}^{k, k'}$        | p[$g, \psi_g^{(i)}, k, k'$]        | 
| $y_g$        | yg[$g$]        | 
| $v_g$        | vg[$g$]        | 
| $p_g$        | pg[$g$]        | 
| $z_g$        | zg[$g$]        | 
| $e_g$        | eg[$g$]        | 
| $l_g$        | lg[$g$]        | 

The name of a solution file is in the format Sol_instance_maxCompTime_nbThreads_initialSolution_validInequalities_run.sol. If no feasible solution is produced, the solution file does not exists.

## impactOperators

The three subfolders include the results deactivating one of the two operators. The formats of the file names are the same as baseALNS. In PerIteration, the useful columns are:
- avgDuration 1: average time consumed by one iteration with the heuristic operator, in seconds
- avgDuration 5: average time consumed by one iteration with the MILP operator, in seconds

## mechanismWeightUpdate

The five subfolders include the results with one of five exponents in the formula of revised weights. The formats of the file names are the same as baseALNS.

# Processing of the results

Folder postProcessing includes the files processing the raw results. 

File formattingTimetable.ipynb can be used to convert the format of the timetables in folder Results from the number of minutes after 7 a.m. to "HH:MM". Folder examplesFormattedTimetables contains a few examples.

Files processingObj.ipynb and processingPerIteration.ipynb are used to produce Tables 2-5 and Figure 2 in the paper.

- Table 2 (Section 5.2): run processingObj.ipynb importing data from results/baseALNS and results/exactMethods
- Table 3 (Section 5.3): run processingPerIteration.ipynb importing data from results/impactOperators
- Figure 2 (Section 5.4): run processingPerIteration.ipynb importing data from results/baseALNS
- Table 4 (Section 5.5): run processingObj.ipynb importing data from results/impactOperators
- Table 5 (Section 5.6): run processingObj.ipynb importing data from results/mechanismWeightUpdate

# References

Lee, K., Jiang, Y., Ceder, A., Dauwels, J., Su, R., & Nielsen, O. A. (2022). Path-oriented synchronized transit scheduling using time-dependent data. Transportation Research Part C: Emerging Technologies, 136, 103505. https://doi.org/10.1016/J.TRC.2021.103505