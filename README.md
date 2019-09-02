# Polarization
This set of functions calculates polarization of light scattered by dust particles in magnetic field using ADDA (https://github.com/adda-team/adda). It is a unix command line application written in c99 and it calls the shell to execute ADDA repeatedly, so it can and will be rather slow.

File description:

vector.c, vector.h describe basic 3D vectors;

orientation.c, orientation.h provide structures and functions describing orientation in 3D space using Euler angles (in zyz notation in accordance with ADDA) and quaternions;

geometry.c, geometry.h constain miscallenious geometric functions such as rotation of a vector;

shape.c, shape.h contain a structure representing geometric characteristics of a scatterer and related functions, the structure is created using an ADDA file;

scatterer.c, scatterer.h describe optical properties of a scatterer;

adda.c, adda.h contain structure and functions used to run ADDA;

polarization.c, polarization.h contain functions used to calculate polarization cross-section of a particle. The particle is thought to be rotating around the axis with maximum momentum of inertia, while the axis itself rotates around the magnetic field line. Hence the polarization cross-section depends on 2 angles: beta - between the magnetic field and the particle axis, and gamma - between the particle axis and the direction of the incident radiation.

modify.c, modify.h allow to modify an existing ADDA scatterer by taking out some of the dipoles to particles in which density depends on the distance to the center.

There are also 2 programs to test the functions:
progtestpol.c compares the calculated polarization cross-section of a small prolate ellipsoid with b/a = 4 with the theoretically predicted by Draine, Lee(1985) C(beta, gamma) = (C_per - Cpar) / 2 * 3/2(cos^2(beta - 1/3)) * sin^2(gamma). It needs to be called with parameters -init initfile, where initfile defines the scatterer and ADDA parameters like path to adda executable (ex - file init in tests).

progtestmod.c modifies the same ellipsoid with parameters: only 0.1 of all dipoles remain, core radius is 0.2 of total, the shell density follows ro(r) = A / r + B (dip/ ADDA volume units), where A = 0.06, B = 0.002, the core is homogenous and contains the remaining material. It needs to by called with parameters -file filename, where filename is an ADDA file to modify.

The tests/small_prolate directory contains results of those programs running:

test_prolate.geom is generated by ADDA with "./adda -shape ellipsoid 1.0 4.0 -m 1.7 0.1 -lambda 100 -eq_rad 0.1 -prop 1 0 0 -save_geom test_prolate.geom";

test_prolate.out contains a table comparing calculated cross-section with predicted by formula for different beta and gamma;

test_prolate_mod.geom contains the modified shape;

test_prolatemod.out contains information about the modification and checks that the shape actually follows the desired density distribution.
