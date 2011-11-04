---
layout: page
title: "minimization and equilibration"
comments: false
sharing: false
footer: false
sidebar: false
---

## Minimization

The first step in simulating our system containing gramicidin A is to perform a minimization. This will remove any interactions
(such as steric clashes between atoms) in the system that might cause the numerical integrators to become unstable when we run dynamics.

Open up the NAMD configuration `$MDT/minimization/MIN00.namd` using `Text Wrangler` or your favorite text editor (but not Word or TextEdit).
This file specifies a number of parameters that NAMD will use to perform the minimization of our model. The only modifications that you will
need to make is to specify the `coordinates` and `structure` parameters (replace the `XXXX` with the proper path to the file). You will
find information about these parameters in the [NAMD manual](http://www.ks.uiuc.edu/Research/namd/2.8/ug/node13.html#SECTION00062100000000000000).
After you have made the necessary modifications, save `MIN00.namd`, open a terminal window and use `cd` to change the current directory to
`$MDT/minimization`.

Run the minimization via:

```bash Terminal
namd2 +p2 MIN00.namd > min00.log &
``` 

This may take several minutes. After the minimization has completed, examine the `.log` file to ensure that the gradient of the energy 
is less than 5.0 by the end of the minimization. You can select the pertinent lines using the unix command `grep`:

```bash Terminal
grep GRADIENT min00.log
```

Next, plot the change in energy of the system as a function of minimization step using VMD. In the `VMD Main` window, open `NAMD Plot` which can be
found under `Extensions > Analysis`. Select the `min.log` file in the `file` pull down menu. We want to consider both the total energy, but also the
contributions from the bond, angle, dihedral, electrostatic and vdw terms. Check each of these boxes and then plot the data using the `File` pull down
menu.

{% blockquote %}
QUESTION 1: Does a single energy term dominate the total change in energy of the system? If so, which one?  
{% endblockquote %}

Now load the `min00.dcd` file into VMD. First load in your initial model `.pdb` file and then use the `Load Data Into Molecule ...` option in
`VMD Main > File` to load the `.dcd` file. You should see small but noticeable changes in your system.

## Heating

The next step is to thermalize the system (i.e heat it to room temperature) and then release the restraints we have placed on the protein and 
the ion in the channel. In the terminal, move to the `$MDT/heating` directory. We will do this in three steps.  
These should run sequentially, waiting for one to finish before starting the next run.

```bash Terminal
namd2 +p2 HEAT00.namd > heat00.log &
namd2 +p2 HEAT01.namd > heat01.log &
namd2 +p2 HEAT02.namd > heat02.log &
``` 
Each simulation should take approximately:

* HEAT00 -- 3 minutes
* HEAT01 -- 25 minutes
* HEAT02 -- 6 minutes

After each run has completed, load the `.dcd` file into VMD to visualize the system. Also use the log file and the `NAMD Plot` plugin that 
you used previously to look at the temperature and volume of the system. Save a copy of each plot
using the `Export to postscript` option to submit along with the answers to the questions. 
Also use the `RMSD Trajectory Tool` under the `VMD Main > Extensions > Analysis` menu to look at the 
[root mean square deviation](http://en.wikipedia.org/wiki/Root_mean_square_deviation_(bioinformatics\)). 
of the protein after doing an alignment to your model reference structure. 

{% blockquote %}
QUESTION 2: Qualitatively describe what is happening to the temperature,volume and rmsd during each simulation 
{% endblockquote %}

## Production

The system should now be at the correct temperature and unrestrained and ready for us to do a long simulation. 
We will run these simulations on a small cluster in the department rather on the workstations in the Hughes Lab, and analyze the
resulting trajectories in the final section of the lab on biomolecular simulation.
