Nucleus Scorer
==============

TOPAS-nBio provides a scorer for the damage produced to a nucleus using the geometry component TsNucleus. The quantity and the component for the scorer need to be specified as follows (assuming that TsNucleus class is named as "Nucleus")::

  s:Sc/DNAScorer/Quantity                                 = "NucleusDNADamage"
  s:Sc/DNAScorer/Component                                = "Nucleus"

Also, to correctly consider all the hits produced in all geometric subcomponents, it is necessary to propagate the scorer to the children components via::

  b:Sc/DNAScorer/PropagateToChildren                      = "true"
  
The parameter 'ScoringRadius' filters the considered hits to be scored, and it is set to the nucleus radius using, for example::

  d:Sc/DNAScorer/ScoringRadius                            = Ge/Nucleus/NucleusRadius um

Deciding in which structures to score
-------------------------------------

A single element of a DNA strand is composed of three different components: base, sugar-phosphate backbone and hydration shell. Also, the double helixes are coiled around proteins called histones that confer structural support to the DNA arrangement. Histones can be used as scavengers for chemical species (via the boolean parameter "HistoneAsScavenger"). One can include physical and chemical interactions with any of these four types of structures. To specify which interactions to include, the lists of considered materials for each strand are specified using the parameters::

  sv:Sc/DNAScorer/Strand1MaterialNames = 4 "G4_WATER_MODIFIED" "G4_BackboneMaterial" "G4_BaseMaterial" "G4_HistoneMaterial"
  sv:Sc/DNAScorer/Strand2MaterialNames = 4 "G4_WATER_MODIFIED" "G4_BackboneMaterial" "G4_BaseMaterial" "G4_HistoneMaterial"

where these materials need to be defined and assigned to each subcomponent in the Nucleus parameter section. Note that "G4_WATER_MODIFIED" is assigned to the hydration shell in our example.

Preparing the proper output
---------------------------

As several subcomponents are created as children of the Nucleus class, the scorer needs to locate at which logical level base pairs (base, backbones and hydration shells) are generated. This is specified by the parameter::

  i:Sc/DNAScorer/BasePairPositionAtGeometricHierarchy = 0

where 0 means that base pairs are the last subcomponent created, i.e., there are no further children. This parameter should not be changed unless the Nucleus class is modified to include subcomponents of bases, backbones or hydration shells.
Geometrical information about the nucleus is also needed to correctly locate the damages along the chromosomes of the nucleus. This information for our nucleus model is provided in the "supportFiles" folder and is passed to the scorer through the parameters::

  s:Sc/DNAScorer/GeometryInfo = "supportFiles/Geoinfo.txt"
  s:Sc/DNAScorer/CopyNoTable = "supportFiles/CopyNoTable.txt"
  s:Sc/DNAScorer/signedCHVoxel = "supportFiles/signedCHVoxel.txt"

Damage scoring
--------------

A strand break (SB) is considered whenever one of the following conditions is met:

* The total energy deposited in a backbone exceeds the specified threshold (direct damage)
* A hydroxil interacts with a backbone, with a given probability to induce damage (indirect damage)
* An ionization is produced in the surrounding hydration shell, which damage is propagated to the backbone (direct damage)

The threshold for direct damage to the backbone is set to 17.5 eV by default but can be specified by the user using the parameter::

  d:Sc/DNAScorer/DamageThreshold = 17.5 eV

Another option is to consider a probability for direct damage linearly increasing with the deposited energy instead of a single threshold. To do so, the following three parameters need to be specified::

  b:Sc/DNAScorer/UseLinearProbabilityThreshold = "true"
  d:Sc/DNAScorer/LinearProbability_lower_limit = 5 eV
  d:Sc/DNAScorer/LinearProbability_upper_limit = 37.5 eV

where the probability of damage is set to 0 for energies lower than or equal to the "LinearProbability_lower_limit"; to 1 for energies greater than or equal to "LinearProbability_upper_limit" and increases linearly between those two values.

The probability for an hydroxil to cause damage once interacting with a backbone is controlled by the parameter::

  u:Sc/DNAScorer/ProbabilityForOHToCauseDamage = 0.4

The propagation of the damage to the hydration shell can be deactivated by the user through the parameter::

  b:Sc/DNAScorer/ScoreOnHydrationShell = "false"
  
Also, one can decide whether to include or not damage to bases in the results by the parameter::

  b:Sc/DNAScorer/ScoreOnBases = "true"
  
Finally, one can exclude direct and/or indirect damages from the results with the parameters::

  b:Sc/DNAScorer/ScoreDirectDamages = "false"
  b:Sc/DNAScorer/ScoreIndirectDamages = "false"
  
A double strand break (DSB) is defined as two SB in complementary strands separated by less than a given number of base pairs. This separation is set to 10 by default but can be controlled by the parameter::

  i:Sc/DNAScorer/DSBSeparation = 10

All SB not included in DSB are scored as single strand breaks (SSB).
Complex damages in the same number of base pairs as specified by "DSBSeparation" are also counted. In particular, we differentiate among two SSBs (SSB+), two DSBs (DSB+) or any other combination of more complex damages. These tallies are independent of the SSB and DSB tallies, i.e., the DSBs in a DSB+ damage still are counted as two DSBs.
  
Outputs
-------

Our scorer offers three different outputs. 
* Firstly, the regular output for nTuple scorers in TOPAS is produced, including an event-by-event description of the energy and dose imparted to the nucleus; the track-averaged LET computed as the energy divided by the track length of the primary particle inside the nucleus; and a complete list of damages (SSB, DSB, SSB+, DSB+, more complex), classified by either direct, indirect or hybrid (in the case of DSB).
* Secondly, the Standard for DNA Damage (SDD) can be produced [1]_. All the fields included in the SDD format is on the `SDD readthedocs`_. Whether to use the minimal SDD output or the complete specification is controlled by the parameter::

  b:Sc/DNAScorer/MinimalSDDOutput = "false"
  
Also, the user can only include those damages including a DSB using the parameter::

  b:Sc/DNAScorer/IncludeDSBDamageSitesOnlyinSDD = "true"
  
* Finally, an exhaustive list of the damage produced in each backbone and base can be obtained in CSV format by setting::
  
  b:Sc/DNAScorer/WriteCSVOutputWithAllDamageSpecification = "true"
  
This list includes all the base pairs affected in each chromosome, with 'D' meaning direct damage; 'I' indirect damage; 'M' multiple damage (direct+indirect); and '*' meaning that some energy has been imparted to that element but not enough to consider a direct damage.

Excluding fragments
-------------------
Finally, an additional feature consists of excluding damages produced in those fragments of DNA (i.e., the DNA between two DSBs) that are shorter than a given threshold. This is controlled by the set of parametes::

  b:Sc/DNAScorer/ExcludeShortFragment = "true"
  i:Sc/DNAScorer/LowerFragmentDetectionThreshold = 250
  i:Sc/DNAScorer/UpperFragmentDetectionThreshold = 300000000

where the thresholds refer to the number of base pairs in each fragment.

.. _SDD readthedocs: https://standard-for-dna-damage.readthedocs.io/en/latest/

References
----------
.. [1] Schuemann, J., McNamara, A. L., Warmenhoven, J. W., Henthorn, N. T., Kirkby, K. J., Merchant, M. J., et al. (2019). A New Standard DNA Damage (SDD) Data Format. Radiation Research, 191(1), 76
