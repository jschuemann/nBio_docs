Introduction
============

**Scorers in TOPAS-nBio**

TOPAS-nBio offers the user a few generic scorers for some geometries as well as specialized scorers for select geometry components. Users also have the option of using the TOPAS scorers.  

Many of our scorers are based on the ntuple scorer of TOPAS, which allows quantities for each event to be scored in columns. This gives you the flexibility to also edit the extension to score any additional information required by the simulation. More information on custom extension components is available `here`_. 


.. warning::
    Note: The DoseToWater and DoseToMaterial scorers do not work with the Geant4-DNA physics processes. Please use the DoseToMedium scorer.

.. _here: https://opentopas.readthedocs.io/en/latest/extension-docs/scoring.html
