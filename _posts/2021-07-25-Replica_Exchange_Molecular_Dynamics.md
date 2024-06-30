---
layout: post
title: Replica Exchange Molecular Dynamics (REMD)
categories: [Research]
---

Replica Exchange Molecular Dynamics (REMD)



One of the bottle necks of vanilla Molecular Dynamics simulation is sampling events that are rare. Examples of rare events are like dipole flipping, enzymatic reactions, nucleation of phases and protein folding. Concretely, anything that happens on a long time scale compared to molecular timescale can be coined as a rare event. 

These events are usually separated by energy barriers more than few k<sub>B</sub>T and are hard to cross. If one wants to study such rare event property of a system, the only way the person through vanilla MD is by simulating the system for a formidable amount of time to get enough statical points to draw some inference. While this itself is a bit overwhelming task to carry out, in some cases this invariably means simulating till eternity. 

To circumvent this problem, many advance sampling algorithms were developed, and REMD is one such algorithm. Just like any algorithm, REMD can only benefit if your requirements are specific to what this algorithm is capable of. Particularly, for sampling rare events at low temperatures like glass dynamics, REMD can be very useful. 

Lets illustrate this through an schematic, where we already know the phase diagram and energy associated for every state point. In practice this diagram is never known ahead of time which is why this method. 

<img src="{{site.baseurl}}/assets/img/2021-07-25-Replica_Exchange_Molecular_Dynamics_img_1.png" />

The mean energy of a system is governed by the temperature, and if the system falls into any one of the two traps in phase space, it is very unlikely of the system to overcome the barrier and visit the other trap at low temperature.  Therefore at low temperature, the simulation will barely sample both. And if the behaviour of the system near the energy barrier is someone's concern, or the different equilibrium that the system can exhibits is what he/she is looking for, then getting good statistics is challenging. 

So in REMD (also called parallel tempering), we simulate two or more systems simultaneously at different temperatures. At certain intervals of time, we swap the systems following swap acceptance ratio and again proceed to carry out the simulation. The time interval between two swaps should be chosen such that the system equilibriates at a temperature properly, and the overall task is carried out till one has done enough sampling. 

![img](https://www.mdpi.com/biomolecules/biomolecules-11-01416/article_deploy/html/images/biomolecules-11-01416-g002-550.jpg)

With more swaps from higher temperature to a lower temperature, the possibility of sampling the system at or near the rare event increases. This apparently strange technique of sampling is justified because the replicas are exchanged following metropolis algorithms.  

$P(\beta U(x))$ is the probability of finding the system with energy $U(x)$. 

Any scheme that satisfies detailed balance forms a Markov chain whose stationary distribution is the target ensemble. 

Thus we are safe as long as we do the swapping abiding the relation:

$ P(\beta, U(x))P((\beta, U(x))$ -> $(\beta', U(x))) = P(\beta', U(x))P((\beta', U(x))$ ->  $(\beta, U(x)))$

But in MD, the system's $U(x)$ will vary with temperature. Therefore, we have to choose a gradient of temperature such that the spacing between the temperatures provide overlapping distribution of U(x). Then swaps are made between the structures of nearest temperature following the Boltzmann acceptance ratio. 

A very fine gradient of temperature is desirable as this will increase the chances of swaps and enhance the sampling. However, from a practical point of view, this will again increase the resource requirement by a huge number and make the technique less effective; as usually each replicas are assigned 1/2 cores of processors in normal practice.  Again if the temperature gradient is very course, then the occurrence of overlapping energy will reduce thereby rejecting most swaps which essentially leads normal MD simulations. Therefore judicious choice of temperature points must be made along with a tolerance. It is a general practice to choose temperatures in geometric progression, and typically the exponential function has been used in many works [$T(i)=T_{0}e^{k*i}$]. The exchange rate of replicas is an important quantity. "Typically 0.2 to 0.3 is good". 

You may enjoy a better and elaborate explanation of the process in [<u>this</u>](https://gpantel.github.io/computational-method/MSST/) blog.  

