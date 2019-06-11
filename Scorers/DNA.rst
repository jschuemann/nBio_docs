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



 
