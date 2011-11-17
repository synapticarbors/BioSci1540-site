---
layout: page
title: "analysis"
comments: false
sharing: false
footer: false
sidebar: false
---

## Visualization of production run

## Ion Position

## Water String Dipole

If you closely examine the string of water in the pore, you will notice that the water molecules are not 
randomly oriented. Instead the oxygen of one water points toward the hydrogens of the next. This directs
the negatively charged oxygen in one direction with the positively charged hydrogens in the other. 

{% blockquote %}
QUESTION 1: Predict what happens to the orientation of the water as postively charged ion like potassium moves through the pore. When the ion is at the center of the pore, will the water above and below it be oriented in different directions?
{% endblockquote %}

We can quantify this orientation by measuring the the [net dipole](http://en.wikipedia.org/wiki/Electric_dipole_moment) of the string of waters.
The following tcl script will calculate the z-component of the net dipole for each snapshot in your trajectory and write the results to a file:

``` tcl Tk Console
set pwat [atomselect top "water and same resid as {water and within 4 of protein and z > -8 and z < 8}"]
set nframes [molinfo top get numframes]

set fout [open dipole.dat w]

for {set k 0} {$k < $nframes} {incr k} {
$pwat frame $k
$pwat update

set dipole [measure dipole $pwat -debye]
set dz [lindex $dipole 2]
puts $fout $dz
}

close $fout

```

Plot the data by launching the application `Grace` which is located in the `Applications` folder.

- Open `dipole.dat` by selecting `Data > Import > ASCII..` and then selecting `dipole.dat` as the file. You may have to navigate to `$MDT/analysis` to locate it.
- Select the options: 
    - `Set type: XY`
    - `Load as: Single Set`
    - Click `OK` to plot the data

{% blockquote %}
QUESTION 2: Does the net dipole change during the simulation? If so, does it change smoothly in time or does it make a sudden jump? Give a qualitative description of what you think is going on in this plot.
{% endblockquote %}

Save an `.jpeg` image of the plot and put it in your analysis directory. To save the image, in `Grace`, under the `File > Print setup..` dropdown,
select `Device: JPEG`, and set the path in the `File name` element. Hit `Apply` then `Accept` and then `Ctrl+P`. You should now see the `.jpeg` file 
in the directory you specified.  

