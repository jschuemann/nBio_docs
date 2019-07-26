Physics Processes
=================
The physical processes of the Geant4-DNA toolkit [Incerti2010]_ [Bernal2015]_ [Incerti2018]_ 
can be used in TOPAS-nBio to perform track-structure simulations via the modules 
``g4em-dna`` and ``g4em-dna_optN`` with N = 1,...,8. To use any of these lists, users need to include one of 
these modules in the modular physics list, e.g.::

 sv:Ph/Default/Modules = 1 "g4em-dna_opt2"

List of Available Modules
-------------------------
It is not trivial to set a default physics list for track-structure Monte Carlo simulations. The lack
of experimental measurements  at nanoscopic scales makes it difficult to 
validate of the existing physics models. Typically, experiments are performed in water-gas, allowing detectors
to be 1000 times larger than for liquid water, however, with potentially different interaction between the water molecules.
New measurements for liquid water at nanoscales as well as a for few other materials are now available and implemented in
Geant4-DNA. 

Geant-DNA provides several constructors
which contain a variety of physics models for scattering processes. Hence, the
selection of a suitable physics list is delegated to the users judgment according to the problem
they want to tackle. A detailed description of each process and associated model available 
in Geant4-DNA is shown `here <http://geant4-dna.in2p3.fr/styled-3/styled-8/index.html>`_. These
models are condensed into several Geant4 constructors as shown 
`here <http://geant4-dna.in2p3.fr/styled-3/styled-9/index.html>`_ The
correspondence between the Geant4-DNA physics constructors and the TOPAS modules is shown 
in the table below. Users who are advanced experts in Geant4 physics can also write 
their own Geant4 physics modules and plug these into TOPAS through the extensions 
interface, see `TOPAS physics list <https://topas.readthedocs.io/en/latest/extension-docs/physics.html>`_.


+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
|  **TOPAS Module Name**      | **Geant4 Class Name**          | **Notes**                                                                              |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
| TsEmDNAPhysics              | N/A                            | Allows to customize physics models per process                                         |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
| TsEmDNAChemistry            | N/A                            | Includes revised chemistry parameters                                                  |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
| TsEmDNAChemistryExtended    | N/A                            | Includes revised chemistry parameters and an extended set of reactions                 |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
| g4em-dna                    | G4EmDNAPhysics                 | Default Geant4-DNA constructor                                                         |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
| g4em-dna_opt1               | G4EmDNAPhysics_option1         |                                                                                        |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
| g4em-dna_opt2               | G4EmDNAPhysics_option2         | Accelerated default Geant4-DNA constructor                                             |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
| g4em-dna_opt3               | G4EmDNAPhysics_option3         |                                                                                        |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
| g4em-dna_opt4               | G4EmDNAPhysics_option4         | See `Kyriakou et al., (2016) <http://dx.doi.org/10.1063/1.4950808>`_                   |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
| g4em-dna_opt5               | G4EmDNAPhysics_option5         |                                                                                        |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
| g4em-dna_opt6               | G4EmDNAPhysics_option6         | See `Bordage et al., (2016) <http://dx.doi.org/10.1016/j.ejmp.2016.10.006>`_           |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
| g4em-dna_opt7               | G4EmDNAPhysics_option7         |                                                                                        |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
| g4em-dna_opt8               | G4EmDNAPhysics_option8         |                                                                                        |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
| g4em-dna-stationary         | G4EmDNAPhysics_stationary      | The kinetic energy of the particle is set to its incident value in inelastic processes |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
| g4em-dna-stationary_opt2    | G4EmDNAPhysics_stationary_opt2 | The kinetic energy of the particle is set to its incident value in inelastic processes |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
| g4em-dna-stationary_opt4    | G4EmDNAPhysics_stationary_opt4 | The kinetic energy of the particle is set to its incident value in inelastic processes |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
| g4em-dna-stationary_opt6    | G4EmDNAPhysics_stationary_opt6 | The kinetic energy of the particle is set to its incident value in inelastic processes |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
| g4em-dna-chemistry          | G4EmDNAChemistry               | Default Geant4-DNA constructor                                                         |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+
| g4em-dna-chemistry_opt1     | G4EmDNAChemistry_opt1          | Includes revised chemistry parameters                                                  |
+-----------------------------+--------------------------------+----------------------------------------------------------------------------------------+




Physics models per region
--------------------------
A region is a Geant4 concept that allows the use of different production cuts 
of secondary particles in different parts of the simulated geometry/world. Geant4-DNA 
extended that capability to use different physical models in different geometry components 
[Ivanchenko2011]_. This allows to delegate the more computationally expensive tracking of detailed particle 
interactions to specific components while using the condensed-history approach elsewhere.
In this way, the simulation can be sped up without compromising accuracy, if the setup is carefully designed. 
This feature is also available in TOPAS-nBio. In order to use this capability, the first step is to assign 
all the components where simulations using the Geant4-DNA physics processes are wanted, to a 
region, e.g.::

 s:Ge/MyComponent1/AssignToRegionNamed     = "DetailedTransport"
 s:Ge/AnotherComponent/AssignToRegionNamed = "DetailedTransport"

Subsequently, an electromagnetic standard physics list is defined in a modular way and the 
Geant4-DNA physics is activated in the region of 
interest (`DetailedTransport` in this example) by the following parameter::

 sv:Ph/Default/Modules = 1 "g4em-penelope"
 s:Ph/Default/ForRegion/DetailedTransport/ActiveG4EmModelFromModule = "g4em-dna"

The example ``G4DNAModelPerRegion.txt`` shows a complete implementation of this capability.

Customizable Physics models 
---------------------------
TOPAS-nBio provides the flexibility to control the model type involved in each process
provided by Geant4-DNA through the module ``TsEmDNAPhysics``. To accomplish this task, 
the energy cut for applying electron capture or electron solvation is automatically 
readjusted according to the lower energy limit of the physical models. In this way, 
it is possible, for example, to combine the elastic models from the CPA100 implementation available 
in ``g4em-dna_opt6`` with the inelastic models from the Emfietzoglou-based implementation 
available in ``g4em-dna_opt4``::
 
 sv:Ph/Default/Modules = 1 "TsEmDNAPhysics"

 s:Ph/Default/Electron/SetElasticScatteringModel   = "CPA100"   
 s:Ph/Default/Electron/SetExcitationModel          = "Emfietzoglou" 
 s:Ph/Default/Electron/SetIonisationModel          = "Emfietzoglou"
 b:Ph/Default/Electron/ActiveVibExcitation         = "True"
 b:Ph/Default/Electron/ActiveAttachment            = "True"

This feature is supported for mainly for electrons and in a restricted way for protons 
(only the elastic scattering model WentzelVI can be chosen instead of the default one). The
example ``ActiveCustomizablePhysics.txt`` shows a complete implementation of this capability.

Variance reduction for e- ionization events
-------------------------------------------
Another capability included in the module ``TsEmDNAPhysics`` is a variance reduction named
flagged uniform particle split. This technique performs uniform splitting to secondary 
electrons produced in ionization events at strategically located regions (defined by
the user) within the geometry and assigns a unique flag number, which is inherited by 
their progeny. The flag permits reclassification of each split event as if they were 
produced by independent histories. This method reduces the variance by improving the 
statistics of secondary electrons, while keeping the time increase small compared to 
the generation of additional particles, by only producing additional electrons in strategically selected 
regions [RamosMendez2017]_. To use this technique, as a first step, the volumes of interest
(where the split will occur) must be assigned to a common region::

  s:Ge/MySplitRegion/AssignToRegionNamed = "SplitRegion"

Then, the variance reduction has to be activated and the region and the number of particle splits must be defined,
in the example below, 100 electrons will be propagated for every 1 electron entering the region::

  b:Vr/UseG4DNAVarianceReduction = "True"
  s:Vr/ParticleSplit/SplitElectronsInRegionNamed = "SplitRegion"
  i:Vr/ParticleSplit/NumberOfSplit = 100 

The scorers used with this technique must be modified to register the contribution of each split
particle independent from other particles using a flag. Two concrete scorers that show how to
use this option are ``TsScoreDBSCAN.cc`` and ``TsScorePDB4DNA.cc``. The associated examples are 
``DBSCAN_VRT.txt`` and ``PDB4DNA_VRT.txt``. These examples show the implementation of this technique
for the calculation of DNA strand breaks.

References
-----------
.. [Ivanchenko2011] Ivanchenko V, Apostolakis J, Bagulya a., et al., 2011 Recent Improvements 
                    in Geant4 Electromagnetic Physics Models and Interfaces `3th Monte Carlo 
                    Conf. MC2010` 2 898–903 `link <http://hal.in2p3.fr/in2p3-00658779>`_
 
.. [RamosMendez2017] Ramos-Méndez J, Schuemann J, Incerti S, Paganetti H, Schulte R and 
                   Faddegon B 2017 Flagged uniform particle splitting for variance 
                   reduction in proton and carbon ion track-structure simulations 
                   `Phys. Med. Biol.` 62 5908–25 `link <http://iopscience.iop.org/0031-9155/62/15/5908>`_

.. [Incerti2010] Incerti S, Ivanchenko A, Karamitros M, et al., 2010 Comparison of GEANT4 very 
                 low energy cross section models with experimental data in water. `Med. Phys.` 37 4692–708

.. [Bernal2015] Bernal M A, Bordage M C, Brown J M C, et al., 2015 Track structure modeling in 
                liquid water: A review of the Geant4-DNA very low energy extension of the Geant4 
                Monte Carlo simulation toolkit. `Phys. Med.` 31 861–74 
                `link <http://www.sciencedirect.com/science/article/pii/S1120179715010042>`_

.. [Incerti2018] Incerti S, Kyriakou I, Bernal M A, et al., 2018 Geant4-DNA example applications 
                 for track structure simulations in liquid water: A report from the Geant4-DNA 
                 Project `Med. Phys.` 45 e722–39 `link <http://doi.wiley.com/10.1002/mp.13048>`_
