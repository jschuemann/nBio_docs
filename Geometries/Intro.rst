Introduction
===================================

**Geometries and Set-Up in TOPAS-nBio**

To cover a wide range of geometries, TOPAS-nBio offers the user a vast catalogue of biological targets. On the micrometer scale, users can select cells by shape or differentiation, choosing to model a single cell or multiple cells (e.g., neuron network or cells *in vitro*). Other micrometer (sub-cellular) scale targets, such as the cell nucleus or mitochondria are also offered as geometries. A single organelle structure may be simulated or multiple organelles can be included in the full cell geometry. 

On the nanometer scale, biologically significant molecules (e.g., DNA, RNA, membrane lipids) are offered as geometries. Since DNA is the primary target of radiation in the cell, realistic DNA models are important for track structure simulations. For this reason, TOPAS-nBio has developed numerous models of DNA in different stages of hierarchal folding within the cell. This includes models of the cell nucleus incorporating the DNA structure from chromatin territories and chromatin fibre models down to the double strand DNA helix. Other potential radiation targets are also considered, for example mitochondrial DNA, membranes and other molecules essential to healthy cell function (RNA, proteins). 

Geometries are designed as custom geometry components in TOPAS. Users have the option of modifying the geometry extension class itself to customize a specific component for their own application or users can interact with the component using the parameter file exclusively. For more information on how to modify geometry components or write your own, see the TOPAS documentation on `custom geometry components`_.

.. _custom geometry components: https://topas.readthedocs.io/en/latest/extension-docs/geometry.html 
