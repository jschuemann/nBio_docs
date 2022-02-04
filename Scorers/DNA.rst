DNA Scorers
===========

Simple SSB and DSB Scorer
-------------------------

For the simple DNA models (e.g. plasmids, linear DNA strand, Charlton DNA strand), a generic scorer for scoring single and double strand breaks (SSB and DSB) is available::

  #Score simple breaks in a DNA strand
  s:Sc/BreakScorer/Quantity = "simpleSSBandDSB" 

The scorer outputs the following quantities::

  Columns of data are as follows:
  1: Event number
  2: DNA parent geometry
  3: Single strand breaks
  4: Double strand breaks


Solenoid Fiber
--------------

For the TsSolenoidFiber geometry, two ntuple scorers are provided. 

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
This scorer will output the number of DSBs in a DSB.dat output file. The scorer also provides output in the SDD (Standard for DNA Damage) format [Schuemann2019]_. All the fields included in the SDD format is on the `SDD readthedocs`_. 

.. _SDD readthedocs: https://standard-for-dna-damage.readthedocs.io/en/latest/


References
----------
.. [Schuemann2019] Schuemann J, McNamara A L, Warmenhoven J W, Henthorn N T, Kirkby K J, Merchant M J, et al. 2019. 
       A New Standard DNA Damage (SDD) Data Format. Radiation Research, 191(1), 76

 
 

 
