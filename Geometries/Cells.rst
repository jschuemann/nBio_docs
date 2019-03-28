Cells
=====

A common end point of measurement for cells cultured *in vitro* in radiobiology is clonogenic cell survival. Single cell Monte Carlo simulations can provide insight into the total dose received by the cell or the distribution of dose within sub-regions of the cell (e.g., dose to the cell nucleus). This may be correlated with experimental measurements of the loss of a specific function or cell death. Single cell models can also form the geometric boundary for more complex studies, for example, calculating the energy deposited in other sub-cellular components (e.g., organelles, cell membranes) or investigating DNA damage in the nucleus. 

TOPAS-nBio provides users with a unique framework for simulating multiple cell types and the option of including organelle sub-components in the model. 

Spherical Cell
---------------
.. figure:: images/SphericalCell.png
   :width: 300
   :align: center 


The TsSphericalCell component is a model of a spherical cell with the option of including organelles. 
To create the model specify the component name as well as the cell radius required::  

  s:Ge/MyCell/Type="TsSphericalCell"
  d:Ge/MyCell/CellRadius=20 um

Users have the option of including a nucleus::

  d:Ge/MyCell/Nucleus/NuclRadius=5. um


Mitochondria can also be placed randomly in the cell, the size and the number of mitochondria are specified in the parameter file::

  i:Ge/MyCell/Mitochondria/NbOfMito=20
  d:Ge/MyCell/Mitochondria/a=0.5 um
  d:Ge/MyCell/Mitochondria/b=0.3 um
  d:Ge/MyCell/Mitochondria/c=0.9 um

A nucleus and mitochondria may be added to any of the cell models listed in a similar manner.


Ellipsoid Cell
--------------
.. figure:: images/EllipsoidCell.png
   :width: 300
   :align: center

The TsEllipsoidCell component is a model of an ellipsoid cell with the option of including organelles. 
To use the model specify the component name as well as the semi-axis lengths required:: 

  s:Ge/MyCell/Type="TsEllipsoidCell"
  d:Ge/MyCell/xSemiAxis=20 um
  d:Ge/MyCell/ySemiAxis=10 um
  d:Ge/MyCell/zSemiAxis=15 um


Cuboidal Cells
--------------
.. figure:: images/CubodialCell.png
   :width: 300
   :align: center 

The TsCuboidalCell component is a model of a cuboidal or rectangular cell with the option of including organelles. 
To use the model specify the component name as well as the half-lengths of each side of the cube required:: 

  s:Ge/MyCell/Type="TsCuboidalCell"
  d:Ge/MyCell/Cell_HLX=10 um
  d:Ge/MyCell/Cell_HLY=20 um
  d:Ge/MyCell/Cell_HLZ=10 um



Fibroblasts
-----------
.. figure:: images/FibroblastCell1.png
   :width: 300
 
.. figure:: images/FibroblastCell2.png
   :width: 300

.. figure:: images/FibroblastCell3.png
   :width: 300

Three models of Fibroblasts are provided in TOPAS-nBio::

  s:Ge/MyCell/Type="TsFibroblastCell1"



Bone Cells
----------

Three different bone cell geometries are available in TOPAS-nBio: osteoblasts, osteoclasts and osteocytes.

Each cell model has it's own geometric component and can be models with::

  s:Ge/MyCell/Type="TsOsteoblast"
   
