Chemistry
==========
Default chemistry list
---------------------------------------------
TOPAS-nBio inherits the parameters (reactions, reaction rates, etc.) provided 
by the Geant4-DNA toolkit to perform radiolysis simulations via the modules 
`g4em-dna-chemistry <https://topas.readthedocs.io/en/latest/parameters/physics/modular.html#list-of-available-modules>`_ 
and 
`g4em-dna-chemistry_opt1 <https://topas.readthedocs.io/en/latest/parameters/physics/modular.html#list-of-available-modules>`_. 
The chemistry parameters provided in the first module are reported in 
[Karamitros2012]_ [Incerti2016]_. The chemistry parameters provided in
the second module are reported in [RamosMendez2018]_.
 
The activation of the chemical stage is performed by including a chemistry model in 
the modular physics list, for example using the following parameter::

 sv:Ph/Default/Modules = 2 "g4em-dna" "g4em-dna-chemistry"

The order of these modules must be consistent with the occurrence of the 
physical and chemical processes. Thus, the module for the physical process
must be followed by the module for the chemical process. By default, the 
pre-chemical and chemical stages are activated, but no explicit step-by-step 
transport is performed. This is useful only for the scoring of the G-value 
(the yield of chemical species per 100 eV of energy deposit) as a function 
of time. A scorer that retrieves this quantity is the ``GValue`` scorer.
The example ``GvalueG4DNADefault.txt`` shows a complete implementation of this
scorer.

Explicit transport of chemical species
---------------------------------------------
The explicit step-by-step transport of chemical species can be activated by
using a set of parameters associated with a chemistry name (analogous to the physics list ``Ph/ListName``). 
Different sets of parameters can be associated to different chemistry names, and only those assigned to the 
chemistry name defined by the following parameter will be used (e.g.
``TOPASChemistry``::

 s:Ch/ChemistryName = "TOPASChemistry"

The configuration of parameters for the step-by-step chemistry 
stage requires the user to define an end time and a time resolution. The end time must be
larger than 1 ps (i.e. the time where the chemical stage begins). The time resolution can be 
defined piecewise. For example, to set the transport of chemical species up to 15 ns with time step resolution of 1 ns from 1 ps to 10 ns and 10 ps from 10 ns to 15 ns,
the following parameters must be set (assuming the chemistry name ``TOPASChemistry``)::

 b:Ch/TOPASChemistry/ChemicalStageTransportActive     = "True" 
 d:Ch/TOPASChemistry/ChemicalStageTimeEnd             = 15.0 ns
 dv:Ch/TOPASChemistry/ChemicalStageTimeStepsHighEdges   = 2 10.0  15.0 ns
 dv:Ch/TOPASChemistry/ChemicalStageTimeStepsResolutions = 2  1.0  10.0 ps 

After these parameters have been set, the spatio-temporal information of the 
chemical species is available and can be visualized using the
TOPAS graphics. A scorer example to retrieve the track information (physical
and chemical) is ``particleTuple.txt``. A graphics example to visualize the transport
of chemical species is ``ActiveChemistryDefault.txt``.

Configurable chemistry list
----------------------------
Simulation of the water radiolysis process requires the setup of a 
large set of parameters: branching ratios, dissociation schemes, 
reaction rates, types of chemical species and diffusion coefficients. 
Advanced users that require to activate or deactivate reactions, change the 
reaction rates or diffusion coefficients, etc., have that flexibility 
provided by TOPAS-nBio. For that, one of the specialized modules ``TsEmDNAChemistry``
and ``TsEmDNAChemistryExtended``  must be 
included instead of the Geant4DNA chemistry module as follows:: 

 sv:Ph/Default/Modules = 2 "g4em-dna" "TsEmDNAChemistry"

or::

 sv:Ph/Default/Modules = 2 "g4em-dna" "TsEmDNAChemistryExtended"

Instead of the ``g4em-dna`` physics module, one of the other options described in the
:ref:`Physics Processes` can be used.

In this way, a whole set of parameters can be customized by using the following 
conventions. Chemical species are named using full names without separation 
spaces, for example, H\ :sub:`2`\ O\ :sub:`2` is HydrogenPeroxide (case 
insensitive). The list of available molecules and diffusion coefficients 
customizable via ``TsEmDNAChemistry`` and ``TsEmDNAChemistryExtended``  are 
shown in the following table:

+--------------------------+--------------------+------------------------------------------------------+
|  Molecule                |   TOPAS name       | D (10\ :sup:`–9`\ m\ :sup:`2`\ /s) at 25\ :sup:`o`\ C|
+--------------------------+--------------------+------------------------------------------------------+
| e\ :sup:`–`\ :sub:`aq`   | SolvatedElectron   |  4.9                                                 |
+--------------------------+--------------------+------------------------------------------------------+
| :sup:`•`\ OH             | Hydroxyl           |  2.2                                                 |
+--------------------------+--------------------+------------------------------------------------------+
| H\ :sup:`•`              | Hydrogen           |  7.0                                                 |
+--------------------------+--------------------+------------------------------------------------------+
| H\ :sub:`3`\ O\ :sup:`+` | Hydronium          |  9.46                                                |
+--------------------------+--------------------+------------------------------------------------------+
| H\ :sub:`2`              | Dyhydrogen         |  4.8                                                 |
+--------------------------+--------------------+------------------------------------------------------+
| OH\ :sup:`–`             | Hydroxide          |  5.3                                                 |
+--------------------------+--------------------+------------------------------------------------------+
| H\ :sub:`2`\ O\ :sub:`2` | HydrogenPeroxide   |  2.3                                                 |
+--------------------------+--------------------+------------------------------------------------------+
| O\ :sub:`2`              | Oxygen             |  2.4       (only in TsEmDNAChemistryExtended)        |
+--------------------------+--------------------+------------------------------------------------------+
| O\ :sub:`2–`             | SuperoxideAnion    |  1.75      (only in TsEmDNAChemistryExtended)        |
+--------------------------+--------------------+------------------------------------------------------+
| HO\ :sub:`2`             | HydroPeroxide      |  2.3       (only in TsEmDNAChemistryExtended)        |
+--------------------------+--------------------+------------------------------------------------------+
| HO\ :sub:`–2`            | Dioxidanide        |  1.4       (only in TsEmDNAChemistryExtended)        |
+--------------------------+--------------------+------------------------------------------------------+

Prechemical stage
~~~~~~~~~~~~~~~~~~~
The dissociation schemes and branching ratios are inherited from Geant4-DNA. 
In general, users do not need to change or set these values. If for any reason
the users require customization of these parameters, the following parameters available
in TOPAS-nBio will facilitate this task (assuming ``s:Ch/ChemistryName = "TOPASChemistry"``)::

 u:Ch/TOPASChemistry/IonizationState/DissociativeDecayProbability = 1.00
 u:Ch/TOPASChemistry/A1B1/DissociativeDecayProbability = 0.65 
 u:Ch/TOPASChemistry/A1B1/RelaxationProbability = 0.35
 u:Ch/TOPASChemistry/B1A1/AutoIonizationProbability = 0.55 
 u:Ch/TOPASChemistry/B1A1/DissociativeDecayProbability = 0.15 
 u:Ch/TOPASChemistry/B1A1/RelaxationProbability = 0.30
 u:Ch/TOPASChemistry/RydbergStatesAndDiffuseBands/AutoIoinizationProbability = 0.5
 u:Ch/TOPASChemistry/RydbergStatesAndDiffuseBands/RelaxationProbability = 0.5

Chemical stage
~~~~~~~~~~~~~~~
For the chemical stage, the number of reactions and reaction rates are also 
inherited from Geant4-DNA. Additional reactions can be defined using the molecules 
from the previous table, and the reaction rates can also be overwritten for the 
existing reactions. The way the reactions are defined is the following, let us
assume we have two molecules named ``SolvatedElectron`` and ``Oxygen``. After 
their reaction, they produce the product ``SuperoxideAnion``. Then, two 
parameters are required to define that reaction: one parameter to 
associate the pair of molecules and define the products, one parameter to
assigns the reaction rate (with units of /M/s), e.g::

 # Define the products:
 sv:Ch/TOPASChemistry/BinaryReaction/SolvatedElectron/Oxygen/Products    = 1 "SuperoxideAnion"

 # Assign a reaction rate value:
 d:Ch/TOPASChemistry/BinaryReaction/SolvatedElectron/Oxygen/ReactionRate = 1.9e10  /M/s

If the reaction does not produce any product or the product won't react further, for 
example,  the product from :sup:`•`\ OH + H\ :sup:`•` –> H :sub:`2`\ O, then the name ``NoProduct`` 
must be used, e.g::

 # Define the reaction without products:
 sv:Ch/TOPASChemistry/BinaryReaction/Hydroxyl/Hydrogen/Products = 1 "NoProduct"

TOPAS-nBio provides two sets of chemical parameters in the files ``TOPASChemistry.txt`` 
and ``TOPASChemistry_Extended.txt`` to be used with ``TsEmDNAChemistry`` and ``TsEmDNAChemistryExtended``
, respectively. These files (available in examples/processes) should be included in the usual way
with ``includeFile = "TOPASChemistry.txt"``. The first set of reactions and reaction rates reproduces
experimental data from the literature, as reported in [RamosMendez2018]_. The examples 
``ActiveChemistryRevised.txt`` and ``ActiveChemistryExtended.txt`` show how to define the new reaction
capability.

Truncation transport for chemical stage
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
An additional feature is the capability to terminate the transport of chemical species in volumes
having a user defined material. In that case, the species are terminated once they come in contact
with the volume (at the boundary or the first step within the volume). To use that capability, a new
material must be cloned from the ``G4_WATER`` material::

 s:Ma/G4_WATER_MODIFIED/CloneFromMaterial = "G4_WATER"
 d:Ma/G4_WATER_MODIFIED/CloneWithDensity  = 1.0 g/cm3

Then call the following parameters (also, see example ``RemoveChemicalSpeciesInVolume.txt``)::
 
 s:Ch/TOPASChemistry/RemoveInMaterial = "G4_WATER_MODIFIED"
 sv:Ch/TOPASChemistry/RemoveInMaterialTheseMolecules = 2 "SolvatedElectron" "Hydrogen"


Independent Reaction Times
---------------------------------------------
The independent reaction times [Clifford1986]_ approximation is implemented in TOPAS-nBio. The chemistry parameters 
consisting on number of reactions, reaction types and reaction rates are those published in the 
reference [Plante2017]_. Current implementation is limited to the calculation of chemical yields. The accuracy
of the TOPAS-nBio implementation has been reported in [Schuemann2019]_. Users interested in to collaborate in testing
and further development of this implementation are encourage to contact to the PI of the TOPAS-nBio collaboration
Dr. Jan Schuemann.


References
-----------
.. [Karamitros2012]  Karamitros M, Mantero A, Incerti S, Friedland W, Baldacchino G, Barberet P, 
                     Bernal M, Capra R, Champion C, El Bitar Z, Francis Z, Gueye P, Ivanchenko A, 
                     Ivanchenko V, Kurashige H, Mascialino B, Moretto P, Nieminen P, Santin G, 
                     Seznec H, Tran H N, Villagrasa C and Zacharatou C 2011 Modeling Radiation 
                     Chemistry in the Geant4 Toolkit Prog. Nucl. Sci. Technol. 2 503–8 
                     `link <http://www.aesj.or.jp/publication/pnst002/data/503-508.pdf>`_
.. [Incerti2016]  Incerti S, Douglass M, Penfold S, Guatelli S and Bezak E 2016 
                     Review of Geant4-DNA applications for micro and nanoscale simulations Phys. 
                     Medica 32 1187–200 `link <http://www.physicamedica.com/article/S1120-1797(16)30927-9/pdf>`_
.. [RamosMendez2018] Ramos-Méndez J, Perl J, Schuemann J, McNamara A, Paganetti H and Faddegon B 
                     2018 Monte Carlo simulation of chemistry following radiolysis with TOPAS-nBio 
                     Phys. Med. Biol. 63 105014 `link <http://iopscience.iop.org/article/10.1088/1361-6560/aac04c>`_
.. [Clifford1986]    Clifford P, Green N J B, Oldfield M J, Pilling M J and Pimblott S M 1986 
                     Stochastic Models of Multi-species Kinetics in Radiation-induced Spurs J. Chem. Soc., Faraday Trans. 1 82 2673–89 `link <http://doi.org/10.1039/F19868202673>`_
.. [Plante2017]      Plante I and Devroye L 2017 Considerations for the independent reaction times and step-by-step 
                     methods for radiation chemistry simulations" Radiat. Phys. Chem. 139 157-172 `link <http://dx.doi.org/10.1016/j.radphyschem.2017.03.021>`_
.. [Schuemann2019]   Schuemann, J, McNamara, A L, Ramos-Méndez, J, Perl, J, Held, K D, Paganetti, H, Incerti, S, 
                     Faddegon, B 2019 TOPAS-nBio: An Extension to the TOPAS Simulation Toolkit for Cellular and 
                     Sub-cellular Radiobiology Radiation Research, 191(2), 125–138 `link <https://wwwncbinlmnihgov/pubmed/30609382>`_

