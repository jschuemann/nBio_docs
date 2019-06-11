Cell Scorer
===========

Organelle Cell Scorer
---------------------

A generic ntuple cell scorer is provided. This scorer allows the user to do scoring in the organelles of the cell::

  #Score energy deposited in Cell components
  s:Sc/OrganelleScorer/Quantity = "NtupleForCell" 

The scorer is compatible with all the cell models and outputs the following data::

  Columns of data are as follows:
  1: Position X [cm]
  2: Position Y [cm]
  3: Position Z [cm]
  4: Energy [MeV]
  5: Energy Deposited [MeV]
  6: Particle Type (in PDG Format)
  7: Track ID
  8: Run ID
  9: Event ID
 10: Volume Name

Users may edit the NtupleForCell extension to edit or add additional output parameters. More information on how to design custom scorers and the n-tuple format can be found on the `TOPAS custom scorers readthedocs`_.


Neuron Scorer
-------------

A ntuple scorer specifically for neuron structures is also available::

  s:Sc/NeuronScorer/Quantity    = "NtupleForNeuron"

This ntuple scorer is an extension that can be edited by the user to add additional output parameters, depending on the application. More information on how to design custom scorers and the n-tuple format can be found on the `TOPAS custom scorers readthedocs`_. The default output of the scorer is::

  Columns of data are as follows:
  1: Position X [cm]
  2: Position Y [cm]
  3: Position Z [cm]
  4: Energy [MeV]
  5: Energy Deposited [MeV]
  6: Particle Type (in PDG Format)
  7: Track ID
  8: Run ID
  9: Event ID
 10: Volume Name
 11: Replica Number

Here, replica number applies to the components comprising the dendrites and axons.

.. _TOPAS custom scorers readthedocs: https://topas.readthedocs.io/en/latest/extension-docs/scoring.html?highlight=tuple

 
