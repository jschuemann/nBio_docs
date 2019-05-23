DNA models
===========
Undeniably the nucleus and the genome it contains is the primary radiation target in the cell and of vital importance in most radiobiology studies. Accurate models of the spatial and temporal distribution of radiation induced damage, in the form of DSBs or lesions, on the full DNA structure and its successive evolution is essential for formulating a full understanding of the biological effects of radiation. 


Simple Cylindrical Targets
--------------------------
DNA has been modeled with Monte Carlo simulations for the last three decades. Originally, energy depositions were modeled within simple cylindrical targets representing DNA stands, nucleosomes or chromatin fibers in order to compare to the experimental microdosimetry data. 

Three simple cylindrical target geometries are available, these represent a chromatin fiber, nucleosome and DNA strand, respectively::

  s:Ge/MyChromatin/Type="TsChromatin"
  s:Ge/MyNucleosome/Type="TsNucleosome"
  s:Ge/MyDNA/Type="TsDNA"  

The size of the cylinders are set to sizes representing each representative geometry. For the DNA strand this is a length of 2 nm with a diameter of 2 nm, the nucleosome has a length of 10 nm and diameter of 5 nm while the chromatin fiber has a length of 25 nm and diameter of 25 nm. Users have the option of specifying new dimensions for each component. 


Charlton DNA Model
------------------
The TsCharltonDNA model is based on a simple combination of cylinders. The inner cylinder has a diameter of 1 nm and length of 0.34 nm, representing the basepair of the DNA strand. Two surrounding half-cylinders represent the sugar phosphate backbone of the DNA; these are each rotated by 36 degrees on adjacent base-pairs:: 

  s:Ge/MyDNA/Type="TsCharlton"


Linear DNA Model
-----------------

A similar model to the Charlton DNA model called TsLinearDNA also models the DNA basepair as a cylinder of diameter 1 nm and length 0.34 nm, but models the sugar phosphate backbone as two quarter cylinders opposite each other with an outer diameter of 2.37 nm, rotated by 36 degrees on each subsequent basepair:: 

  s:Ge/MyDNA/Type="TsLinearDNA"
 


Circular Plasmid
-----------------


Non-circular Plasmid
---------------------

Solenoid Chromatin Fiber Model
-------------------------------


Fractal DNA model
-----------------

Full DNA model - Geant4-DNA
----------------------------
