---
layout: page
title: "model building"
comments: false
sharing: false
footer: true
sidebar: false
---

## Some initial notes

- Throughout the tutorial, **terminal** refers to the application in `/Applications/Utilities`.
- The **Tk Console** is a terminal-like window that you can access in `VMD` via the drop down menu `Extensions` in the `VMD Main` window.
- The VMD `atomselect` command requires that you either specify a `molid` or `top` (e.g. `atomselect top "SELECTION"` or `atomselect 3 "SELECTION").
If you are using `top` as is used below, make sure that the `T` appears next to the correct model 


## Download files

Begin by downloading the archive of files from lab github repository, which can be found at:
[https://github.com/synapticarbors/BioSci1540/zipball/master](https://github.com/synapticarbors/BioSci1540/zipball/master)

Move the `.zip` archive from where it was downloaded (most likely the Downloads directory) to a new folder that you will use for
the remainder of the labs. Preferably, this will be in a location that is backed-up to the class server so that you can access it
on other machines in the lab. Once you have moved the archive, which will be named something like `synapticarbors-BioSci1540-XXXXX.zip`,
you can extract the files by either double-clicking on it and then rename the folder `md_tutorial`, or opening a terminal window and running the following commands:

``` bash Terminal
cd <Location of .zip file>
unzip synapticarbors-BioSci1540-XXXXX.zip
mv synapticarbors-BioSci1540-XXXXX md_tutorial
```
Be sure to replace the `XXXXX` in `synapticarbors-BioSci1540-XXXXX` by whatever the names of the files are for your specific case.
For the rest of the lab, the path to the `md_tutorial` directory will be written in shorthand as `$MDT`. 

## Construct initial model for gramicidin in membrane and solvent

If we were setting up a system entirely from scratch, we would likely begin with a set of coordinates from the [Protein Data Bank](http://www.pdb.org/).
The experimental structure might be missing the coordinates for a subset of the residues, and would be lacking solvent and lipids and probably would not contain
the positions of any hydrogens in the protein.
Due to the time constraints of the lab, you will begin with a system in which gramicidin has already been embedded in a DMPC lipid bilayer with water
and ions, although hydrogen atoms have not been added yet. Take a moment to look at the page for 1JNO structure of Gramicidin A on the PDB website, whose coordinates provide the initial structure of the protein.
[http://www.pdb.org/pdb/explore/explore.do?structureId=1JNO](http://www.pdb.org/pdb/explore/explore.do?structureId=1JNO) 


{% blockquote %}
QUESTION 1: What method was used to determine the structure of 1JNO?  
{% endblockquote %}

### Create topology file and add hydrogens

While the `.pdb` file tells `NAMD` the initial coordinates to begin the simulation with, we also need to provide it with a topology 
file that tells it which atoms are bonded, which are connected via an angle spring, etc. The topology file carries a `.psf` extension and
can be created using the `psfgen` tool that comes with VMD. This tool can also be used to add hydrogen atoms to the system.

In the `Tk Console`, first change directories to `$MDT` and then load the starting coordinates:

``` tcl Tk Console
cd $MDT/model_files
mol new gramicidin_initial_struct.pdb
```

Take a moment to look at the structure, change the representation to highlight the protein, lipid, water, and ions.

The first thing we have to is split the structure into segments and create a new `.pdb` file for each.
We will use the VMD command `atomselect` to select a subset of atoms and then write that subset to a `.pdb` file

``` tcl Tk Console
set wat [atomselect top "water"]
$wat writepdb wat.pdb

set lip [atomselect top "lipid"]
$lip writepdb lipids.pdb

set ga1 [atomselect top "segname GA1"]
$ga1 writepdb  ga1.pdb

set ga2 [atomselect top "segname GA2"]
$ga2 writepdb  ga2.pdb

set cl [atomselect top "resname CLA"]
$cl writepdb  cl.pdb

set pot [atomselect top "resname POT"]
$pot writepdb  pot.pdb
```

Now load `psfgen`

``` tcl Tk Console
package require psfgen
resetpsf
```

Load the topology file that shows how the atoms in different residues and groups should be built, and define
some aliases:

``` tcl Tk Console
topology ../toppar/top_all27_prot_lipid_gram.inp

pdbalias residue DLE LEU
pdbalias residue DVA VAL
pdbalias atom ETA O OG
```

Define the segments:

``` tcl Tk Console
segment GA1 {
first NONE
last NONE
pdb ga1.pdb
}

segment GA2 {
first NONE
last NONE
pdb ga2.pdb
}

segment TIP3 {
first NONE
last NONE
pdb wat.pdb
}

segment MEMB {
first NONE
last NONE
pdb lipids.pdb
}

segment CLA {
first NONE
last NONE
pdb cl.pdb
}

segment POT {
first NONE
last NONE
pdb pot.pdb
}
```

Load in the coordinates from the `.pdb` files we previously created:

``` tcl Tk Console
coordpdb ga1.pdb GA1
coordpdb ga2.pdb GA2
coordpdb wat.pdb TIP3
coordpdb lipids.pdb MEMB
coordpdb cl.pdb CLA
coordpdb pot.pdb POT
```
Guess the coordinates of any atoms specified by the topology file, but missing in the coordinate files (e.g. hydrogen atoms):

``` tcl Tk Console
guesscoord
```

and finally write out the new coordinates and topology file:

``` tcl Tk Console
writepsf gramicidin_final_model.psf
writepdb gramicidin_final_model.pdb
```

Now load the new model into VMD to visualize it and make sure everything looks good:

``` tcl Tk Console
mol new gramicidin_final_model.psf
mol addfile gramicidin_final_model.pdb
```

{% blockquote %}
QUESTION 2: Did psfgen do a good job of selecting the positions of the hydrogen atoms for the water? Why or why not? (HINT: It may be easier to visualize the water if it is shown in the VDW representation). 
{% endblockquote %}

### Place a potassium atom in the channel

In order to investigate the behavior of a potassium ion (K+) in the channel, each group in the lab will place an ion at a different position in the channel.
We will do so by swapping one of the K+ atoms in the bulk solvent for a water molecule in the channel.

First determine the extent of the protein by selecting the backbone atoms and then measure their minimum and maximum values along the z-direction:

``` tcl Tk Console

```
