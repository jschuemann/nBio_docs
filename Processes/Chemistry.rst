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
 
The activation of the chemical stage is then delegated to the following 
parameter::

 sv:Ph/Default/Modules = 2 "g4em-dna" "g4em-dna-chemistry"

The order of these modules must be consistent with the occurrence of the 
physical and chemical processes. Then, the module for the physical process
must be followed by the module for the chemical process. By default, the 
pre-chemical and chemical stages are actived, but not explicit step-by-step 
transport is performed. This is useful only for the scoring of the G-value 
(the yield chemical of species per 100 eV of energy deposit) as a function 
of the time. A scorer that retrieves this quantity is the ``GValue`` scorer.
The example ``GvalueG4DNADefault.txt`` shows a complete implementation of this
scorer.

Explicit transport of chemical species
---------------------------------------------
The explicit step-by-step transport of chemical species can be activated by
using a set of parameters associated to a chemistry name (in an analog way 
to the physics list ``Ph/ListName``). Different sets of parameters can be 
associated to different chemistry names, and only those assigned to the 
chemistry name called by the following parameter will be used (e.g.
``TOPASChemistry``::

 s:Ch/ChemistryName = "TOPASChemistry"

The configuration of parameters for the step-by-step chemistry 
stage requires to define: end time and time resolution. The end time must be
larger than 1 ps (init time for the chemical stage). The time resolution can be 
defined piecewise. For example, to set the transport of chemical species up to 15 ns with time 
steps resolution of 1 ns from 1 ps to 10 ns and 10 ps from 10 ns to 15 ns,
the following paremeters must be set (assuming the chemistry name ``TOPASChemistry``)::

 b:Ch/TOPASChemistry/ChemicalStageTransportActive     = "True" 
 d:Ch/TOPASChemistry/ChemicalStageTimeEnd             = 15.0 ns
 dv:Ch/TOPASChemistry/ChemicalStageTimeStep           = 2 10.0  15.0 ns
 dv:Ch/TOPASChemistry/ChemicalStageTimeStepResolution = 2  1.0  10.0 ps 

After these parameters have been set, the spatio-temporal information of the 
chemical species is available and can be visualized using the
TOPAS graphics. A scorer example to retrieve the track information (physical
and chemical) is ``particleTuple.txt``. A graphics example to visualize the transport
of chemical species is ``ActiveChemistryDefault.txt``.

Configurable chemistry list
---------------------------
Simulation of the water radiolysis process requires the setup of a 
large set of parameters: branching ratios, dissociaction schemes, 
reaction rates, type of chemical species  and diffusion coefficients. 
Advanced users that require to activate or deactivate reactions, change the 
reaction rates or diffusion coefficients, etc., have that flexibility 
provided by TOPAS-nBio. For that, one of the specialized modules ``TsEmDNAChemistry``
and ``TsEmDNAChemistryExtended``  must to be 
included instead of the Geant4DNA chemistry module as follows:: 

 sv:Ph/Default/Modules = 2 "g4em-dna" "TsEmDNAChemistry"

or::

 sv:Ph/Default/Modules = 2 "g4em-dna" "TsEmDNAChemistryExtended"

In this way, a whole set of parameters can be customized by using the following 
conventions. Chemical species are named using full names without separation 
spaces, for example, H\ :sub:`2`\ O\ :sub:`2` is HydrogenPeroxide (case 
unsensitive). The list of available molecules and diffusion coefficients 
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
~~~~~~~~~~~~~~~
The dissociation schemes and branching ratios are inherit from Geant4-DNA. 
In general, users do not need to change or set these values. If for any reason
the users require customization of these parameters, then the following 
will facilicate such task (assuming ``s:Ch/ChemistryName = "TOPASChemistry"``)::

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
inherit from Geant4-DNA. Additional reactions can be defined using the molecules 
from the previous table, and the reaction rates can also be overided for the 
existing reactions. The way the reactions are defined is the following, let 
assume the two molecules named ``SolvatedElectron`` and ``Oxygen``. After 
their reaction, they produce the products ``SuperoxideAnion``.Then, two 
parameters are required to define that reaction: one parameter to 
associate the pair of molecules and define the products, one parameter to
assigns the reaction rate (with units of /M/s), e.g::

 # Define the products
 sv:Ch/TOPASChemistry/BinaryReaction/SolvatedElectron/Oxygen/Products    = 1 "SuperoxideAnion"

 # Assign a reaction rate value
 d:Ch/TOPASChemistry/BinaryReaction/SolvatedElectron/Oxygen/ReactionRate = 1.9e10  /M/s

If the reaction does not produce any product or the product won't react further, for 
example,  the product from :sup:`•`\ OH + H\ :sup:`•` –> H :sub:`2`\ O, then the name ``NoProduct`` 
must be used, e.g::

 # Define the reaction without products 
 sv:Ch/TOPASChemistry/BinaryReaction/Hydroxyl/Hydrogen/Products = 1 "NoProduct"

TOPAS-nBio provides two sets of chemical parameters in the files ``TOPASChemistry.txt`` 
and ``TOPASChemistry_Extended.txt`` to be used with ``TsEmDNAChemistry`` and ``TsEmDNAChemistryExtended``
, respectively. These files (available in examples/processes) should be included in the usual way
with ``includeFile = "TOPASChemistry.txt"``. The first set of reactions and reaction rates reproduces
experimental data from the literature, as reported in [RamosMendez2018]_. The examples 
``ActiveChemistryRevised.txt`` and ``ActiveChemistryExtended.txt`` show how to set the previous
capability.

Truncation transport for chemical stage
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
An additional feature is the capability to terminate the transport of chemical species in volumes
having a user defined material. In that case, the species are terminated once they enter in contact
with the volume (at boundary or first step within the volume). To use that capability, a new
material must be clone from the ``G4_WATER`` material::

 s:Ma/G4_WATER_MODIFIED/CloneFromMaterial = "G4_WATER"
 s:Ma/G4_WATER_MODIFIED/CloneWithDensity  = 1.0 g/cm3

Then call the following parameters (also, see example ``RemoveChemicalSpeciesInVolume.txt``)::
 
 s:Ch/TOPASChemistry/RemoveInMaterial = "G4_WATER_MODIFIED"
 sv:Ch/TOPASChemistry/RemoveInMaterialTheseMolecules = 2 "SolvatedElectron" "Hydrogen"


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

