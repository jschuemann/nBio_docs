DNA Scorers
============

TOPAS-nBio provides a scorer for the damage induced to several DNA structures using the step-by-step method to simulate molecule diffusion and chemical reactions. This scorer can be used to simple DNA structures, such as the Charlton DNA model, circular and linear plasmids, simple DNA cylinders. To use this scorer, the following parameter is required::

  s:Sc/DNADamageScorer/Quantity = "DNADamageStepByStep"
  
We provide another parameter file with all the options for the scorer and explanations about these. This file is located at the path: 'examples/scorers/DNAScorerStepByStep.txt'. To use it, please include this file in your parameter file with the adequate path reference. For example, in the example 'FullDNADamageInPlasmid.txt' at the path 'examples/scorers/SBSDamageToDNAPlasmid/', the following parameter is included::
 
  includeFile = ../DNAScorerStepByStep.txt

Damage scoring
--------------

A strand break (SB) is considered whenever one of the following conditions is met:

* The total energy deposited in a backbone exceeds the specified threshold (direct damage)
* A hydroxil interacts with a backbone, with a given probability to induce damage (indirect damage)
* An ionization is produced in the surrounding hydration shell, which damage is propagated to the backbone (direct damage)

The threshold for direct damage to the backbone can be specified by the user using the parameter::

  d:Sc/DNADamageScorer/DirectDamageThreshold = 11.75 eV     # For half-cylindrical DNA model, the recommended value is 17.5 eV

Another option is to consider a probability for direct damage linearly increasing with the deposited energy instead of a single threshold. To do so, the following three parameters need to be specified::

  b:Sc/DNADamageScorer/UseLinearProbabilityForDirectDamage = "true"
  d:Sc/DNADamageScorer/LowerLimitForLinearProbabilityFunction = 5 eV
  d:Sc/DNADamageScorer/UpperLimitForLinearProbabilityFunction = 37.5 eV

where the probability of damage is set to 0 for energies lower than or equal to the "LinearProbability_lower_limit"; to 1 for energies greater than or equal to "LinearProbability_upper_limit" and increases linearly between those two values.

Since v1.1, there are two methods available to score indirect damage, either (a) specifying a single probability for a hydroxyl radical to cause damage once they encounter a backbone or a base; or (b) specifying a distinct probability for the hydroxyl radical to interact once they encounter backbones or bases, and then another distinct probability to produce a break only if the radical interacts with the DNA component. In the first option, radicals are always removed scavenged when they face a DNA component, whereas in the second option radicals are allowed to continue on their way if they happen not to interact. Details and the reasoning behind these two methods can be found in [1]_. Users can select one method or the other with the boolean parameter::

  b:Sc/DNADamageScorer/AlwaysScavengeSpeciesInDNAComponents = "false"

If it is set to "false", the probability for an hydroxyl to cause damage once interacting with a backbone is controlled by the parameter::

  u:Sc/DNAScorer/ProbabilityForOHToCauseDamage = 0.4

If it is set to "true", the probability for a radical to interact with (being scavenged by) a backbone, and the probabilities for a radical to cause damage once it interacts with backbone and bases are controlled by:: 
  
  u:Sc/DNADamageScorer/ProbabilityOfScavengingInBackbone = 0.25 # For half-cylindrical DNA model, the recommended value is 0.0585
  u:Sc/DNADamageScorer/ProbabilityOfScavengingInBase = 1.0
  u:Sc/DNADamageScorer/ProbabilityOfIndirectDamageToBackbone = 0.55 # If always scavenging in DNA components, the recommended value is 0.4
  u:Sc/DNADamageScorer/ProbabilityOfIndirectDamageToBase = 1.0 # If always scavenging in DNA components, the recommended value is 0.4
  
Note that these parameters should be adapted to the DNA geometry model employed (see [1]_).

Since v1.1, a new parameter to handle quasi-direct damage (i.e., damage as a consequence of charge transfer from the solvated water shell to the backbone) can be controlled by the parameter::

  u:Sc/DNADamageScorer/ProbabilityOfChargeTransferFromHydrationShellToBackbone = 0.3333333

Finally, one can exclude direct, quasi-direct and/or indirect damages from the results with the parameters::

  b:Sc/DNADamageScorer/ScoreDirectDamages = "true"
  b:Sc/DNADamageScorer/ScoreIndirectDamages = "true"
  b:Sc/DNADamageScorer/ScoreQuasiDirectDamages = "true"
  
A double strand break (DSB) is defined as two SB in complementary strands separated by less than a given number of base pairs. This separation is set to 10 by default but can be controlled by the parameter::

  i:Sc/DNADamageScorer/MaximumBasePairDistanceToConsiderDSB = 10

All SB not included in DSB are scored as single strand breaks (SSB).
Complex damages in the same number of base pairs as specified by "DSBSeparation" are also counted. In particular, we differentiate among two SSBs (SSB+), two DSBs (DSB+) or any other combination of more complex damages. These tallies are independent of the SSB and DSB tallies, i.e., the DSBs in a DSB+ damage still are counted as two DSBs.

Besides scoring within a stretch of DNA (10 base pairs or the specified length), 'foci' of DSB separated within a given spatial distance can be also counted. This can be managed by the parameters::

  b:Sc/DNADamageScorer/ScoreNumberOfFoci = "true"
  dv:Sc/DNADamageScorer/FociSizes = 2 150 500 nm # Maximum of 5 sizes
  
Each focus is created by the boolean union of spheres with radius equal to those given by the "FociSizes" parameter around each DSB. Up to 5 sizes can be used in a single simulation to calculate the correspondent number of foci. These numbers are also included in the default output of this scorer.

Preparing the proper output
---------------------------

As several subcomponents are created as children of the primary geometry component, the scorer needs to locate at which logical level base pairs (base, backbones and hydration shells) are generated. This is specified by the parameter::

  i:Sc/DNADamageScorer/BasePairPositionAtGeometricHierarchy = 0

where 0 means that base pairs are the last subcomponent created, i.e., there are no further children.

Stopping scoring at a given dose
---------------------------------

An additional parameter has been added to score only the damage resulting after a given dose is delivered. This can be specified by means of::

  d:Sc/DNADamageScorer/StopTrackingAtDose = 0.5 gray
  
Please note that this parameter does not stop the simulation and the remaining histories will go on whenever this dose to the scoring component is achieved. However, all particles in these remaining histories will be stopped at their first interaction within the scoring component.

Outputs
-------

Our scorer offers four different outputs. 

1. The regular output for nTuple scorers in TOPAS is produced, including an event-by-event description of the energy and dose imparted to the nucleus; the track-averaged LET computed as the energy divided by the track length of the primary particle inside the nucleus; and a complete list of damages (SSB, DSB, SSB+, DSB+, more complex and foci), classified by either direct, indirect or hybrid (in the case of DSB). However, one can decide whether to include damages classified by their origin by means of::

    b:Sc/DNADamageScorer/BreakDownOutputPerDamageOrigin	= "true"

2. The Standard for DNA Damage (SDD) can be produced [2]_. All the fields included in the SDD format is on the `SDD readthedocs`_. Whether to use the minimal SDD output or the complete specification is controlled by the parameter::

    b:Sc/DNADamageScorer/MinimalSDDOutput = "false"
  
Also, the user can only include those damages including a DSB using the parameter::

  b:Sc/DNADamageScorer/IncludeDSBDamageSitesOnlyinSDD = "true"
  
3. An exhaustive list of the damage produced in each backbone and base can be obtained in CSV format by setting::
  
    b:Sc/DNADamageScorer/WriteCSVOutputWithAllDamageSpecification = "true"
  
This list includes all the base pairs affected in each chromosome, with 'D' meaning direct damage; 'I' indirect damage; 'M' multiple damage (direct+indirect); and '*' meaning that some energy has been imparted to that element but not enough to consider a direct damage.

4. A visual output of 3D and 2D foci images can be provided. These images are obtained by locating all the DSBs and convolving them with a Point Spread Function (PSF), which blurs each one of the DSBs in all directions. In v2.0, only an isotropic Gaussian function can be used as PSF, although the width (sigma parameter) of the Gaussian function can be specified. Users can select what 2D planes to obtain, and the spatial resolution for the calculated images. For the 2D images, several images with different resolutions can be obtained after a single simulation. This is controlled by the following set of parameters (by default at the DNAScorerStepByStep.txt auxiliary file)::

    b:Sc/DNADamageScorer/Get3DFociImage	= "true"
    b:Sc/DNADamageScorer/Get2DFociImages = "true"
    sv:Sc/DNADamageScorer/2DFociImagePlanes = 3 "X" "Y" "Z"
    s:Sc/DNADamageScorer/MicroscopePSFShape = "Gaussian"
    d:Sc/DNADamageScorer/MicroscopePSFWidth = 400 nm
    dv:Sc/DNADamageScorer/Foci2DImageResolutions = 2 25 100 nm
    d:Sc/DNADamageScorer/Foci3DImageResolution = 400 nm

Images are given in csv files, in which the first columns indicate the indexes of the image matrix (i, j, k for 3D images; i, j for 2D images) and the last column provides the pixel value for the correspondent indexes. The extent of the 2D images on each plane can be specified by the parameters::

  d:Sc/DNADamageScorer/ImageXMinPosition = 0 um - Ge/Nucleus/NucleusRadius
  d:Sc/DNADamageScorer/ImageXMaxPosition = Ge/Nucleus/NucleusRadius um
  d:Sc/DNADamageScorer/ImageYMinPosition = 0 um - Ge/Nucleus/NucleusRadius
  d:Sc/DNADamageScorer/ImageYMaxPosition = Ge/Nucleus/NucleusRadius um
  d:Sc/DNADamageScorer/ImageZMinPosition = 0 um - Ge/Nucleus/NucleusRadius
  d:Sc/DNADamageScorer/ImageZMaxPosition = Ge/Nucleus/NucleusRadius um
  
A python-based script is accompanying the example at 'examples/scorers/SBSDamageToDNANucleus' to visualize the 3D and 2D images generated.

Excluding fragments
-------------------
Finally, an additional feature consists of excluding damages produced in those fragments of DNA (i.e., the DNA between two DSBs) that are shorter than a given threshold. This is controlled by the set of parametes::

  b:Sc/DNADamageScorer/ExcludeShortFragment = "true"
  i:Sc/DNADamageScorer/LowerThresholdForFragmentDetection = 250
  i:Sc/DNADamageScorer/UpperThresholdForFragmentDetection = 300000000

where the thresholds refer to the number of base pairs in each fragment.

Deciding in which structures to score
-------------------------------------

A single element of a DNA strand is composed of three different components: base, sugar-phosphate backbone and hydration shell. Also, the double helixes are coiled around proteins called histones that confer structural support to the DNA arrangement. Histones can be used as scavengers for chemical species (via the boolean parameter "HistoneAsScavenger"). One can include physical and chemical interactions with any of these four types of structures. To specify which interactions to include, the lists of considered materials for each strand are specified using the parameters::

  sv:Sc/DNADamageScorer/Strand1MaterialNames = 4 "G4_WATER_MODIFIED" "G4_BackboneMaterial" "G4_BaseMaterial" "G4_HistoneMaterial"
  sv:Sc/DNADamageScorer/Strand2MaterialNames = 4 "G4_WATER_MODIFIED" "G4_BackboneMaterial" "G4_BaseMaterial" "G4_HistoneMaterial"

where these materials need to be defined and assigned to each subcomponent in the Nucleus parameter section. Note that "G4_WATER_MODIFIED" is assigned to the hydration shell in our example.

Nucleus
=======
Damage to the fractal DNA in a nucleus can be scored using the geometry component TsNucleus. This scorer is a sub-version of the more general DNADamageStepByStep component, and it still uses the same auxiliary parameter file, 'DNAScorerStepByStep.txt'. Check example on 'examples/scorers/SBSDamageToDNANucleus'. The quantity and the component for the scorer need to be specified as follows (assuming that TsNucleus class is named as "Nucleus")::

  s:Sc/DNADamageScorer/Quantity = "DNADamageNucleusStepByStep"
  s:Sc/DNADamageScorer/Component = "Nucleus"

Also, to correctly consider all the hits produced in all geometric subcomponents, it is necessary to propagate the scorer to the children components via::

  b:Sc/DNADamageScorer/PropagateToChildren = "true"
  
The parameter 'ScoringRadius' filters the considered hits to be scored, and it is set to the nucleus radius using, for example::

  d:Sc/DNADamageScorer/ScoringRadius = Ge/Nucleus/NucleusRadius um

Geometrical information about the nucleus is also needed to correctly locate the damages along the chromosomes of the nucleus. This information for our nucleus model is provided in the "supportFiles" folder and is passed to the scorer through the parameters::

  s:Sc/DNADamageScorer/GeometryInfo = "supportFiles/Geoinfo.txt"
  s:Sc/DNADamageScorer/CopyNoTable = "supportFiles/CopyNoTable.txt"
  s:Sc/DNADamageScorer/signedCHVoxel = "supportFiles/signedCHVoxel.txt"

Solenoid Fiber
==============

As an additional scorer independent of the DNADamageStepByStep scorer, two ntuple scorers are provided for the TsSolenoidFiber geometry. 

NtupleForBasePair scores energy deposition quantities within the DNA structure. The scorer is initialized::

  s:Sc/MyScorer/Quantity = "NtupleForBasePair" 
  s:Sc/MyScorer/Component = Fiber

Within the scorer, users can also specify the damage mode::

  s:DamageMode = "Range"              
  d:MinEneRange = 5.0 eV              #Min energy for damage range (Default = 5 eV)
  d:MaxEneRange = 37.5 eV             #Max energy for damage range (Default = 37.5 eV)
  d:MinEneThresh = 17.5 eV            #Min energy for damage threshold (Default = 17.5 eV)
  
Other options for damage mode are "Range"=energy range, "Threshold"=energy threshold, "Ionisation"=ionisations.

Output columns are::

  Columns of data are as follows:
  1: Position X [nm]
  2: Position Y [nm]
  3: Position Z [nm]
  4: Energy [eV]
  5: Event ID
  6: Run ID
  7: Strand Num
  8: IsIonisation
  9: BP
  10: IsBack
  11: IsBase

NtupleForSpatial is another option for scoring damage within the Fiber structure::

  s:Sc/MyScorer/Quantity = "NtupleForSpatial"

Similarly to the previous scorer the damage mode should be set, with the same options "Range"=energy range, "Threshold"=energy threshold and "Ionisation"=ionisations. 
This scorer will output the number of DSBs in a DSB.dat output file. The scorer also provides output in the SDD (Standard for DNA Damage) format [2]_. All the fields included in the SDD format is on the `SDD readthedocs`_. 

.. _SDD readthedocs: https://standard-for-dna-damage.readthedocs.io/en/latest/

References
----------
.. [1] Bertolet, A., Ramos-Mendez, J., McNamara, A., Yoo, D., Ingram, S., Henthorn, N., Warmenhoven, J. W., Faddegon, B., Merchant, M., McMahon S. J. (2022). Impact of DNA geometry and scoring on Monte Carlo track-structure simulations of initial radiation induced damage. Radiation Research, Submitted.
.. [2] Schuemann, J., McNamara, A. L., Warmenhoven, J. W., Henthorn, N. T., Kirkby, K. J., Merchant, M. J., et al. (2019). A New Standard DNA Damage (SDD) Data Format. Radiation Research, 191(1), 76
