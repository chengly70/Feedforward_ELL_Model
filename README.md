# FeedforwardELLModel
Leaky integrate-and-fire (LIF) model of superficial/intermediate pyramidal cell with intrinsic and network heterogneiety, receiving delayed feedforward E (granule) and I (interneuron) input. 
Model of the ELL in the weakly electric fish.

This directory contains Matlab and C program files to implement the computational model in the following paper.

Citation: Ly & Marsat (2017). Variable synaptic strengths controls the firing rate distribution in feedforward neural networks.  XXX

Stored .mat files to generate the same plots in the paper:
1) The mat file storedPerm.mat is called for each run; this is a random permutation of Ne=1000 that is used in generating correlated q and Threshold vectors via the function crtW_corr.m 
2) The mat file conRnd_A.mat is the network configuration used in the paper, with Nei=200 in the feedforward population and Ne=1000 in the target superficial pyramidal cells.  The connectivity matrices are saved here, with fixed in-degree that are randomly generated.
3) The experimental data results are stored in ELLdata_April_11_2017.mat , containing the firing rates of all 15 sup/int pyramidal ON cells with both sinusoidal frequencies.  The following .fig files are based on this data: Data_std.fig, low_frq.fig, high_frq.fig 
NOTE: one can re-run the model and obtain our results without using the mat files: conRnd_A.mat and storedPerm.mat; these are simply provided to EXACTLY reproduce the figures in our paper.

Main LIF Models: 
1) The main LIF spiking model is implemented with a mex (Matlab executable written in C) function: mx_tt_FFpyr.c — see comments in file for details of inputs/outputs.  The main scripts to generate different firing rate distributions as the correlation of q & Thres vary are in scpt_BG_internrn.m (green curve in Fig 3A) and scpt_SM_internrn.m (black curve in Fig 3A).  
2) The altered LIF model with structured connectivity rules in Figure 4 & 5) are found in the sub-directory: ~/clustering/ 
The main driver files are scpt_Netw1_int.m and scpt_Netw2_int.m — they call on the same mex function mx_ff_FFpyr.c and getTunedSinInp_internrn.m.   Instances of the connectivity matrices used in the paper are provided (Parms_clust_int.mat Parms_wedge_int.mat), were created by: gen_hetConn_Parms.m (see file; must comment out ftuning_wedge.m/ftuning_aff.m AND the save commands to get Parms_[clust/wedge]_int.mat).

MUST compile mex function (mx_tt_FFpyr.c) in Matlab, creating a platform dependent function that can be run in Matlab. In Matlab, type: 

mex mx_tt_FFpyr.c

and copy the resulting Matlab executable file to the main directory and any desired sub-directories

Figure 2: the model PSTH as a function of sinusoidal phase is generated by: getPSTH_cycl.m (120Hz sin input)  and getPSTH_cycl_Sm.m (5Hz sin input) these scripts save the results in .mat files (see script files for .mat file names).
Both scripts call the function getSinInp_internr.m which generates the feedforward input from the granule cells (consisting of both E & I cells); getPSTH_cycl.m calls ff_LTsim_BGcyc.m which is the Matlab version of the model — this is so that a smaller time step is used to give a more accurate PSTH with 120Hz (the mex function only saves firing stats every 1ms, although a smaller time step of 0.1ms is used).  The low frequency PSTH cycle histogram can use a larger 1ms time step from mex function.

To create the plots in Figure 2B and 2C, run the script plot_CyclHist.m 

Figure 3: Inset in part A is generated from the file Thry_rhyth.m, a simple implementation of the analytic theory.  Part A is from the 2 main scripts mentioned above: scpt_BG_internrn.m (saves results in d_Bg_int_July17.mat) and scpt_SM_internrn.m (saves results in d_Sm_int_July17.mat), 
which can easily be plotted for example with the following commands AFTER .mat files are generated:

figure; hold on;
load d_Sm_int_July17
plot(corr_preTh,std(fRt_vc),'k.-',’MarkerSize',18)
load d_Bg_int_July17
plot(corr_preTh,std(fRt_vc),'.-','color',[0 .5 0],'MarkerSize',18)

Figure 3: part B is generated by disp_matchDataThry.m
Figure 3: part C were generally manually; schematics.  

Figure 4: See sub-directory ~/clustering/ 
Fig 4A was drawn manually (schematic).  Figure 6B&C generated from plotSurf_qTh_Act_frq.m (comment to use particular Parms_[clust/wedge]_int.mat file)
Fig 4D created by running: disp_netA_thry.m (after running scpt_Netw1_int.m scpt_Netw2_int.m)
Fig 4E created by running: disp_EffectCorr.m (calls calc_effCorrDrive.m)

Figure 5: See sub-directory ~/clustering/ 
Fig 5A is created by running tuningCrvs_beta.m (2nd figure is used, first figure is complete with all digitized frequencies).
Fig 5B is simply the number of presynaptic connections, sorted, so can easily be plotted via Parms_[clust/wedge]_int.mat files, using Matlab’s sum(—,2) and sort() functions.

Figure 6: Showing individual results from Figure 3A with the script disp_indivFR_varyCorr.m; loads data d_Bg_int_July17.mat or d_Sm_int_July17.mat; must 
manually change/comment-out on lines 3,4 to get low or high frequency plots
