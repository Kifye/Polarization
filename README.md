# Polarization
This set of functions calculates polarization of light scattered by dust particles in magnetic field using ADDA (https://github.com/adda-team/adda). It is a unix command line application written in c99 and it calls the shell to execute ADDA repeatedly, so it can and will be rather slow.

The goal is to calculate polarization cross-section of a modelled dust particle in magnetic field. The particle is thought to be rotating around the axis with maximum momentum of inertia, while the axis itself rotates around the magnetic field line. Hence the polarization cross-section depends on 2 angles: beta - between the magnetic field and the particle axis, and gamma - between the particle axis and the direction of the incident radiation. So the function polarization_cross_section just integrates (Cpar - Cper) (calculated by ADDA) over the particle and axis rotation.

It is also possible to modify existing homogenous shapes generated by ADDA by taking out dipoles to simulate particles with heterogenous mass distribution (density depends on the radius).

# Running the program
At this moment there are 3 programs that can be used to test parts of the project.

They have been tested using the file test_prolate.geom generated by adda using command line

`./adda -shape ellipsoid 1.0 4.0 -m 1.7 0.1 -lambda 100 -eq_rad 0.1 -prop 1 0 0 -save_geom test_prolate.geom`

which represents the dipoles of a prolate ellipsoid with y / x = 1, z / x = 4

1) Testing polarization cross-section calculations

To create the executable use console command  

`make`

or 

`make testpol`

It will create the testpol executable that can be run with a command line option -init initfile, where initfile is a file describing adda and scatterer parameters. One such file for example is file "init". 

`./testpol -init init > test_prolate.out`

It creates file test_prolate.out which contains some meta information and a table with polarization cross-sections calculated for different gamma and beta angles and their comparison with predicted by Draine for small prolate spheroids. 

***Warning: this is a rather long calculation.***

2) Testing particle modification

To create the executable use console command  

`make testmod`

It will create the testmod executable that can be run with a command line option -file filename.geom, where filename.geom is a file generated by adda which contains dipoles of the uniform particle to modify.

At this moment modification creates a particle with homogenous core and a shell with density - radius dependance 

ro(r) = A / r + B 

with parameters: A = 0.06, B = 0.002, core_radius/full_radius = 0.2, remaining dipoles / starting  = 0.1.

`./testmod -file test_prolate.geom > test_prolatemod.out`

It creates file test_prolate_mod.geom containing the modified shape and test_prolatemod.out containing some information conserning modification, namely distribution of dipoles by layers and a check whether the resulting density follows the dependance above.

3) Testing basic geometric functions.

It is an auxiliary program that can be used to test functions from vector.c, orientation.c and geometry.c
To create an executable run

`make testgeom`

To run the program

`./testgeom`

File progtestgeom.c can be modified to test different functions. At this moment it creates vector v = {1, 2, 3} and a structure for orientation with euler angles eo = {PI / 23.0, PI / 13.0, PI / 16.0}, then checks the conversion to quaternion and back and rotates the vector using euler angles and quaternion.

# File description:

vector.c, vector.h describe basic 3D vectors;

orientation.c, orientation.h provide structures and functions describing orientation in 3D space using Euler angles (in zyz notation in accordance with ADDA) and quaternions;

geometry.c, geometry.h constain miscallenious geometric functions such as rotation of a vector;

shape.c, shape.h contain a structure representing geometric characteristics of a scatterer and related functions, the structure is created using an ADDA file;

scatterer.c, scatterer.h describe optical properties of a scatterer;

adda.c, adda.h contain structure and functions used to run ADDA;

polarization.c, polarization.h contain functions used to calculate polarization cross-section of a particle. 

modify.c, modify.h allow to modify an existing ADDA scatterer by taking out some of the dipoles to particles in which density depends on the distance to the center.

There are also 2 programs to test the functions:
progtestpol.c compares the calculated polarization cross-section of a small prolate ellipsoid with b/a = 4 with the theoretically predicted by Draine, Lee(1985) C(beta, gamma) = (C_per - Cpar) / 2 * 3/2(cos^2(beta - 1/3)) * sin^2(gamma). It needs to be called with parameters -init initfile, where initfile defines the scatterer and ADDA parameters like path to adda executable (ex - file init).

progtestmod.c modifies the same ellipsoid with parameters: only 0.1 of all dipoles remain, core radius is 0.2 of total, the shell density follows ro(r) = A / r + B (dip/ ADDA volume units), where A = 0.06, B = 0.002, the core is homogenous and contains the remaining material. It needs to by called with parameters -file filename, where filename is an ADDA file to modify.

4 files show the results of these programs running:

test_prolate.geom is generated by ADDA with "./adda -shape ellipsoid 1.0 4.0 -m 1.7 0.1 -lambda 100 -eq_rad 0.1 -prop 1 0 0 -save_geom test_prolate.geom";

test_prolate.out contains a table comparing calculated cross-section with predicted by formula for different beta and gamma;

test_prolate_mod.geom contains the modified shape;

test_prolatemod.out contains information about the modification and checks that the shape actually follows the desired density distribution.
