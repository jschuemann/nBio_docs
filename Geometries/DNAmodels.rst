DNA models
===========
Undeniably the nucleus and the genome it contains is the primary radiation target in the cell and of vital importance in most radiobiology studies. Accurate models of the spatial and temporal distribution of radiation induced damage, in the form of DSBs or lesions, on the full DNA structure and its successive evolution is essential for formulating a full understanding of the biological effects of radiation. 


Simple Cylindrical Targets
--------------------------

.. figure:: images/CylinderDNA.png
   :width: 300
   :align: center 


DNA has been modeled with Monte Carlo simulations for the last three decades. Originally, energy depositions were modeled within simple cylindrical targets representing DNA stands, nucleosomes or chromatin fibers in order to compare to the experimental microdosimetry data. Further details can be found in Nikjoo et al. (1989) and Nikjoo et al. (1991). 

Three simple cylindrical target geometries are available, these represent a chromatin fiber (yellow), nucleosome (red) and DNA strand (green), respectively::

  s:Ge/MyChromatin/Type="TsCylindericalChromatin"
  s:Ge/MyNucleosome/Type="TsCylindericalNucleosome"
  s:Ge/MyDNA/Type="TsCylindericalDNA"  

The size of the cylinders are set to fixed sizes of the representative geometry. For the DNA strand this is a length of 2 nm with a diameter of 2 nm, the nucleosome has a length of 10 nm and diameter of 5 nm while the chromatin fiber has a length of 25 nm and diameter of 25 nm. However, users do have the option of specifying new dimensions for each component with the following parameters::


  d:Ge/MyChromatin/ChromatinHalfLength=12.5 nm
  d:Ge/MyChromatin/ChromatinRadius=12.5 nm

  d:Ge/MyNucleosome/NucleosomeHalfLength=5 nm
  d:Ge/MyNucleosome/NucleosomeRadius=2.5 nm
  	
  d:Ge/MyDNA/DNAHalfLength=1 nm
  d:Ge/MyDNA/DNARadius=1 nm


Nikjoo H, Goodhead D T, Charlton D E & Paretzke H G 1989 Energy deposition in small cylindrical targets by ultrasoft X-rays Phys. Med. Biol. 34(6), 691–705.

Nikjoo H, Goodhead D T, Charlton D E & Paretzke H G 1991 Energy deposition in small cylindrical targets by monoenergetic electrons Int. J. Radiat. Biol. 60(5), 739–756.


Charlton DNA Model
------------------

.. figure:: images/Charlton.png
   :width: 300
   :align: center 


The TsCharltonDNA model is based on a simple combination of cylinders. More details can be found in Charlton, Nikjoo & Humm (1989). The inner cylinder has a diameter of 1 nm and length of 0.34 nm, representing the basepair of the DNA strand. Two surrounding half-cylinders represent the sugar phosphate backbone of the DNA; these are each rotated by 36 degrees on adjacent base-pairs. Users have to specify the number of base pairs to be simulated:: 

  s:Ge/MyDNA/Type="TsCharltonDNA"
  i:Ge/MyDNA/NumberofBasePairs=10


Charlton D E, Nikjoo H & Humm J L 1989 Calculation of initial yields of single- and double-strand breaks in cell nuclei from electrons, protons and alpha particles Int. J. Radiat. Biol. 56(1), 1–19.

Linear DNA Model
----------------

.. figure:: images/LinearDNA.png
   :width: 300
   :align: center 

A similar model to the above Charlton DNA model is called TsLinearDNA which also models the DNA basepair as a cylinder of diameter 1 nm and length 0.34 nm, but models the sugar phosphate backbone as two quarter cylinders opposite each other with an outer diameter of 2.37 nm, rotated by 36 degrees on each subsequent basepair:: 

  s:Ge/MyDNA/Type="TsLinearDNA"
  i:Ge/MyDNA/NumberofBasePairs=10


Circular Plasmid
----------------

.. figure:: images/Plasmid.png
   :width: 300
   :align: center 

TsPlasmid is a simple circular plasmid. The DNA has the same structure as the linear DNA model but arranged in a ring. Each DNA segment consists of a central cylindrical basepair (diameter 1 nm and length 0.34 nm) surrounded by two quarter cylinders (diameter 2.37 nm) for the sugar phosphate backbone. Users have to specify the number of basepairs::

  s:Ge/CircularPlasmid/Type     = "tsplasmid"
  #Define the number of base pairs in the ring
  i:Ge/CircularPlasmid/NumberOfBasePairs = 2000


Supercoiled Plasmid
-------------------

.. figure:: images/SupercoiledPlasmid.png
   :width: 300
   :align: center 

TsSupercoiledPlasmid is a supercoiled plasmid defined from an ascii file that contains the vertex position (in nm) of a deformed polygon that forms the supercoiled path. Different configurations of DNA can be chosen: half cylinders (halfCyl), quarter cylinders - similar to the circular plasmid (QuartCyl) or spheres (Sphere). The DNA consists of the sugar phosphate backbone and base. Users have to specify the file name of the ascii file that contains the vertex of the deformed polygon forming the supercoiled plasmid. We provide two files pBR322_a.xyz and pBR322_b.xyz. For these files, supercoiling is achieved using the Vologodovskii methodology.::

  s:Ge/SupercoiledPlasmid/Type     = "tsplasmidsupercoiled"
  #Define the file name 
  i:Ge/SupercoiledPlasmid/FileName = "pBR322_a.xyz"
  # Define the DNA model
  s:Ge/SupercoiledPlamid/DNA_Model = "QuartCyl" # HalfCyl or Sphere

If each coordinate in the ascii file belongs to a single basepair of the plasmid, then the following must be set:: 
  
  b:Ge/SupercoiledPlamid/SegmentPlasmidPath = "False" # Default False

Otherwise, the deformed polygon is segmented using units of 0.34 nm and smoothed.


Solenoid Chromatin Fiber Model
------------------------------
TsSolenoidFiber is a chromatin fiber model, based on a solenoid geometry, described in `Henthorn et al. (2017)`_.

By default the model of the double helix has backbones and bases modeled as spheres wrapped around a cylindrical histone protein. The radius of the base sphere is 0.208 nm, while the backbone sphere had a radius of 0.240 nm. Users can also specify to build the DNA backbone and base volumes as half cylinders, described by Charlton, Nikjoo and Humm (1989), or as quarter cylinders, as described by Bernal and Liendo (Med. Phys. 2009). The histone radius is 3.3 nm with a length of 5.7 nm. The double helix structure is wrapped around cylindrical histones in 1.65 left-handed turns to form the nucleosome. These are arranged in a solenoid chromatin conformation, with 6 histones per turn of the fiber. Each fiber contains 61 histones and ~10.8 kbp of DNA.
 
Users can set the fiber radius and length::

  d:Ge/Fibre/FibreHalfLength = 80.0 nm
  d:Ge/Fibre/FibreRadius = 18.5 nm
 
Users can change the number of histones per turn::

  i:Ge/Fibre/nb_HistPerTurn = 6
 
Users can change the DNA volume geometry, by including one of the below::

  s:Ge/Fibre/DNA_Model = Sphere            #Described by Henthorn et al. (2017)
  s:Ge/Fibre/DNA_Model = HalfCyl           #Described by Charlton, Nikjoo and Humm (1989)
  s:Ge/Fibre/DNA_Model = QuartCyl          #Described by Bernal and Liendo (2009)
 


Bernal & Liendo 2009 An investigation on the capabilities of the PENELOPE MC code in nanodosimetry Med. Phys. 36(2), 620-625.

Charlton D E, Nikjoo H & Humm J L 1989 Calculation of initial yields of single- and double-strand breaks in cell nuclei from electrons, protons and alpha particles Int. J. Radiat. Biol. 56(1), 1–19.

Henthorn N et al. (2017) Nanodosimetric Simulation of Direct Ion-Induced DNA Damage Using Different Chromatin Geometry Models. Radiation Research, 188, 770-783.


Geant4-DNA Full Nuclear Model
-----------------------------

.. figure:: images/Geant4WholeNucleus.png
   :width: 300
   :align: center

The Geant4-DNA model represents the whole genome (6 x 10^9 bps) within an ellipsoid nucleus for a cell in the G0/G1 phase and is further described in Dos Santos M et al. (2014) Progress in Nuclear Science and Technology 4, 449–453.

The DNA double helix strands are composed of two separate strands built from the union of spheres. The sugar-phosphate backbone of the DNA has a total diameter of 2.16 nm and the DNA base, within the backbone structure, has a diameter of 0.34 nm. The double helix is used to form a nucleosome which consists of a core histone protein (cylinder with diameter 6.5 nm and length of 5.7 nm) wrapped by two turns of the DNA double helix (a total of 200 bps). The chromatin fibre is represented by a cylinder of diameter 30.8 nm and length 161 nm. Each fibre contains 90 nucleosomes which are placed on a helix. To represent the chromatin loops, 7 chromatin fibers are arranged in a "flower" shape. The flower has 7 "petals", with each composed of 4 fibers arranged in a diamond. The flower substructures fill 23 chromosome territories, each represented by a box of varying size. 

Users need to include the files of data specifying the position of the chromosome territories within the nucleus. These files are named chromo*.dat (24 files total). 

To build the full DNA hierarchy, the flags to specify the building of the chromatin fibers and the basepairs should be set to true::  

  s:Ge/MyCell/Type="tsdna"
  b:Ge/MyCell/BuildChromatinFiber="true"
  b:Ge/MyCell/BuildBases="true" 



.. _Henthorn et al. (2017): https://www.rrjournal.org/doi/abs/10.1667/RR14755.1
