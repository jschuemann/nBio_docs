Physics Processes
=================
The physical processes of the Geant4-DNA toolkit [Incerti2010]_ [Bernal2015]_ [Incerti2018]_ 
can be used in TOPAS-nBio to perform track-structure simulations via the modules 
``g4em-dna`` and ``g4em-dna_optN`` with N = 1,...,8. Then, users need to include one of 
these modules in the modular physics list, e.g.::

 sv:Ph/Default/Modules = 1 "g4em-dna_opt2"

Physics models per region
--------------------------
A region is a Geant4 development that allows the use of different production cuts 
of secondary particles in different parts of the simulated geometry. Geant4-DNA 
extended that capability to use different physical models in different geometry components 
[Ivanchenko2011]_. This allows delegates the more expensive tracking of detailed particle 
interactions to specific components. In this way, the simulation speed up. This feature 
is available in TOPAS-nBio. In order to use this capability, the first step is to assign 
all the components where the Geant4-DNA physics processes are more significant, to a 
region, e.g.::

 s:Ge/MyComponent1/AssignToRegionNamed     = "DetailedTransport"
 s:Ge/AnotherComponent/AssignToRegionNamed = "DetailedTransport"

Subsequently, electromagnetic standard physics is defined in a modular way and the region of 
interest (`DetailedTransport` in this example) is used in the following parameter::

 sv:Ph/Default/Modules = 1 "g4em-penelope"
 s:Ph/Default/ForRegion/DetailedTransport/ActiveG4EmModelNamed = "g4em-dna"

The example ``G4DNAModelPerRegion.txt`` shows a complete implementation of this capability.

Customizable Physics models 
---------------------------
TOPAS-nBio provides the flexibility to control the model type involved in each process
provided by Geant4-DNA through the module ``TsEmDNAPhysics``. To accomplish this task, 
the energy cut for applying electron capture or electron solvation is automatically 
readjusted according to the lower energy limit of the physical models. In this way, 
it is possible to combine the elastic models from the CPA100 implementation available 
in ``g4em-dna_opt6`` with the inelastic models from the Emfietzoglou-based implementation 
available in ``g4em-dna_opt4``::
 
 sv:Ph/Default/Modules = 1 "TsEmDNAPhysics"

 s:Ph/Default/Electron/SetElasticScatteringModel   = "CPA100"   
 s:Ph/Default/Electron/SetExcitationModel          = "Emfietzoglou" 
 s:Ph/Default/Electron/SetIonisationModel          = "Emfietzoglou"
 b:Ph/Default/Electron/ActiveVibExcitation         = "True"
 b:Ph/Default/Electron/ActiveAttachment            = "True"

This feature is supported for mainly for electrons and in a restricted way for protons 
(only the elastic scattering model WentzelVI over the default one can be chosen). The
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
the generation of additional particles, by only producing them in strategically selected 
regions [RamosMendez2017]_. To use this technique, as a first step, the volumes of interest
(where the split will occur) must be assigned to a common region::

  s:Ge/MySplitRegion/AssignToRegionNamed = "SplitRegion"

Then, the variance reduction must be activated and the region and number of split must be defined::

  b:Vr/UseG4DNAVarianceReduction = "True"
  s:Vr/ParticleSplit/SplitElectronsInRegionNamed = "SplitRegion"
  i:Vr/ParticleSplit/NumberOfSplit = 100 

The scorers used with this technique must be modified to register the contribution of each split
particle independent from other particles using a flag. Two concrete scorers that show how to
use that flat are ``TsScoreDBSCAN.cc`` and ``TsScorePDB4DNA.cc``. The associate examples are 
``DBSCAN_VRT.txt`` and ``PDB4DNA_VRT.txt``. These examples show the implementation of this technique
in the calculation of DNA strand breaks.

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
