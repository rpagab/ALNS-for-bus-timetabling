# Optimisation of Bus Timetables: An Adaptive Large Neighbourhood Search-based Matheuristic with a Novel Operator Weight

Authors:
Robin Gaborit a, Evelien van der Hurk a, Otto Anker Nielsen a, Yu Jiang a, b, 1
a Department of Technology, Management and Economics, Technical University of Denmark, Denmark
b Lancaster University Management School, Lancaster University, United Kingdom

The current version contains the input data, the parameter settings and the raw results. The future version will include the Julia source code.

# Description

We developed a metaheuristic solution method to solve the model proposed by Lee et al. (2022). This is an acyclic bus timetabling problem with time-dependent travel time and demand data.

# Input data

The study focuses on three bus lines operating within the metropolitan area of Copenhagen, Denmark. These lines, designated 1A, 2A, and 3A, represented some of the most prominent bus routes in the city. We employ a dataset comprising the Danish Rejsekort smart card transactions from 2014. A total of nine instances were produced based on this network. Each instance is named by one letter S, M or L and one number 1, 2 or 3. The letter indicates if the instance size is Small, Medium or Large. Files related to input data are in folder data.

Folder data includes files config_S, config_M, and config_L that contains parameters common to all instances of the same size(durations are in minutes):
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
- weight_latearrival: coefficient of later arrival time in the objective function
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
- sigmas = 1_0.005_1e-8: the rewards $\sigma_1\_\sigma_2\_\sigma_3$
- tinit = 100: the initial temperature $T_{ini}$
- tfin = 100: the final temperature $T_{fin}$
- validInequalities = 0: 0 indicates the valid inequalities are not included in the MILP operator

The following parameters differ between experiments using our ALNS solution method:
- operatorSet: 11 indicates both the MILP and the heuristic operator are used; 01 indicates the heuristic operator is removed; 10 indicated the MILP operator is removed
- exponentForTime: the exponent for time in the formula of the revised weights (two for the inverse-square rule)

# Implementation

The future version will include the Julia source code.

# Results

Folder results contains five subfolders:

## avgCompTimes

This is the average computation time of running an operator for two hours for ten runs of the nine instances, in seconds. The average results are presented in section 5.3, Table 3 of the paper.

## baseALNS

Each file contains the objective function value produced by one run of the ALNS for one instance and the base parameters (full operator set and inverse-square rule). The name of a file is in the format Obj_instance_maxCompTime_nbThreads_operatorSet_lmax_sigmas_tinit_tfin_exponentForTime_validInequalities_run.txt. Besides other results, these results are used to produce Tables 2, 4, and 5.

## exactMethods

The six subfolders contains the results with the six exact methods (same notations as in Section 5.2.2). The name of a file is in the format Obj_instance_maxCompTime_nbThreads_initialSolution_validInequalities_run.txt. These results are used to produce Tables 2 in Section 5.2.2.

## impactOperators

The format of the file names is the same as baseALNS. These results are used to produce Table 4 in Section 5.5.

## mechanismWeightUpdate

The format of the file names is the same as baseALNS. These results are used to produce Table 5 in Section 5.6.



# References

Lee, K., Jiang, Y., Ceder, A., Dauwels, J., Su, R., & Nielsen, O. A. (2022). Path-oriented synchronized transit scheduling using time-dependent data. Transportation Research Part C: Emerging Technologies, 136, 103505. https://doi.org/10.1016/J.TRC.2021.103505