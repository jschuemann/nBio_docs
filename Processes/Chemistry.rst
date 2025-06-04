Chemistry
==========

TOPAS-nBio Default chemistry list
---------------------------------------------
We recommend the following chemistry parameters which include changes that 
allowed to reconcile the experimental and simulated yields of chemical species 
at the picosecond level as shown in [RamosMendez2021]_.

The default chemistry list for TOPAS-nBio can be found at ``topas-nBio/examples/processes/TOPASDefaultReactions.txt``.

To use the default chemistry parameters from TOPAS-nBio, users should specify ``TsEmDNAChemistry``
as the chemistry module. In addition, add the following parameter::

 includeFile = TOPASDefaultReactions.txt

The example ``topas-nBio/examples/scorers/IRTGetGValue/TsIRTGvalue.txt`` shows how to properly use this chemistry list 
and recreate the validation results from [RamosMendez2021]_.

Geant4 Default chemistry list
---------------------------------------------
TOPAS-nBio [Schuemann2019a]_ can inherit the parameters (reactions, reaction rates, etc.) provided 
by the Geant4-DNA toolkit to perform radiolysis simulations via the modules 
`g4em-dna-chemistry <https://opentopas.readthedocs.io/en/latest/parameters/physics/modular.html#list-of-available-modules>`_ 
and 
`g4em-dna-chemistry_opt1 <https://opentopas.readthedocs.io/en/latest/parameters/physics/modular.html#list-of-available-modules>`_. 
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
The example ``topas-nBio/examples/scorers/SBSGetGValue/GvalueG4DNADefault.txt`` shows a complete implementation of this
scorer.

Actvating the Chemistry of TOPAS-nBio
---------------------------------------------
Simulation of chemical species reactions can be activated by
using a set of parameters associated with a chemistry name (analogous to the physics list ``Ph/ListName``). 
Different sets of parameters can be associated to different chemistry names, and only those assigned to the 
chemistry name defined by the following parameter will be used (e.g.
``TOPASChemistry``::

 s:Ch/ChemistryName = "TOPASChemistry"

The configuration of parameters for the chemistry stage requires the user to define an end time 
and (in the case of the Step-By-Step method) a time resolution. The end time must be larger 
than 1 ps (i.e. the time where the chemical stage begins). The time resolution can be defined piecewise. 
For example, to set the transport of chemical species up to 15 ns with time step resolution of 
1 ns from 1 ps to 10 ns and 10 ps from 10 ns to 15 ns, the following parameters must be set 
(assuming the chemistry name ``TOPASChemistry``)::

 # Activate Chemistry and Set Chemistry End Time
 b:Ch/TOPASChemistry/ChemicalStageTransportActive     = "True" 
 d:Ch/TOPASChemistry/ChemicalStageTimeEnd             = 15.0 ns

 # Only for Step-By-Step Chemistry
 dv:Ch/TOPASChemistry/ChemicalStageTimeStepsHighEdges   = 2 10.0  15.0 ns
 dv:Ch/TOPASChemistry/ChemicalStageTimeStepsResolutions = 2  1.0  10.0 ps 

After these parameters have been set, the spatio-temporal information of the 
chemical species is available and can be visualized using the
TOPAS graphics for Step-By-Step chemistry only. A scorer example to retrieve the track information (physical
and chemical) is ``particleTuple.txt``. A graphics example to visualize the transport
of chemical species is ``ActiveChemistryDefault.txt``.

Configurable chemistry list
----------------------------
Simulation of the water radiolysis process requires the setup of a 
large set of parameters: branching ratios, dissociation schemes, 
reaction rates, types of chemical species and diffusion coefficients. 
Advanced users that require to activate or deactivate reactions, change the 
reaction rates or diffusion coefficients, etc., have that flexibility 
provided by TOPAS-nBio. This can be done by including ``TsEmDNAChemistry`` 
instead of the Geant4DNA chemistry module as follows:: 

 sv:Ph/Default/Modules = 2 "TsEmDNAPhysics" "TsEmDNAChemistry"

Instead of the ``TsEmDNAPhysics`` physics module, one of the other options described in the
:ref:`Physics Processes` can be used.

In this way, a whole set of parameters can be customized by using the following 
conventions. Chemical species are named using full names without separation 
spaces, for example, H\ :sub:`2`\ O\ :sub:`2` is HydrogenPeroxide (case 
insensitive). The list of available molecules and diffusion coefficients 
customizable via ``TsEmDNAChemistry`` are shown in the following table:

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
| O\ :sub:`2`              | Oxygen             |  2.4                                                 |
+--------------------------+--------------------+------------------------------------------------------+
| O\ :sub:`2–`             | SuperoxideAnion    |  1.75                                                |
+--------------------------+--------------------+------------------------------------------------------+
| HO\ :sub:`2`             | HydroPeroxy        |  2.3                                                 |
+--------------------------+--------------------+------------------------------------------------------+
| HO\ :sub:`–2`            | Dioxidanide        |  1.4                                                 |
+--------------------------+--------------------+------------------------------------------------------+
| O\ :sub:`3`\ P           | AtomicOxygen       |  2.0       (only for IRT)                            |
+--------------------------+--------------------+------------------------------------------------------+
| O\ :sup:`–`              | Oxyde              |  2.0       (only for IRT)                            |
+--------------------------+--------------------+------------------------------------------------------+
| O\ :sub:`3` \ :sup:`-`   | Trioxide           |  2.0       (only for IRT)                            |
+--------------------------+--------------------+------------------------------------------------------+
| O\ :sub:`3`              | Ozone              |  2.0       (only for IRT)                            |
+--------------------------+--------------------+------------------------------------------------------+

Prechemical stage
~~~~~~~~~~~~~~~~~~~
The dissociation schemes and branching ratios are inherited from Geant4-DNA. 
In general, users do not need to change or set these values. 
Users can select different branching ratio models as follows::
    
    s:Ch/TOPASChemistry/BranchingRatiosModel = "topasnbio" # "g4dna" "experimental"

The default branching ratios in TOPAS-nBio are specified by ``topasnbio``.
The default branching ratios from Geant4-DNA are specified by ``g4dna``. And ``experimental``
can be used if you are interested in testing different ratios. Modification of these branching ratios can
be accomplished as follows (assuming ``s:Ch/ChemistryName = "TOPASChemistry"``)::

 s:Ch/TOPASChemistry/BranchingRatiosModel = "experimental"
 
 u:Ch/TOPASChemistry/BranchingRatios/IonizationState/DissociativeDecay = 1.00
 u:Ch/TOPASChemistry/BranchingRatios/A1B1/DissociativeDecay = 0.65 
 u:Ch/TOPASChemistry/BranchingRatios/A1B1/Relaxation = 0.35
 u:Ch/TOPASChemistry/BranchingRatios/B1A1/AutoIonization = 0.55 
 u:Ch/TOPASChemistry/BranchingRatios/B1A1/DissociativeDecay = 0.15 
 u:Ch/TOPASChemistry/BranchingRatios/B1A1/Relaxation = 0.30
 u:Ch/TOPASChemistry/BranchingRatios/RydbergStatesAndDiffuseBands/AutoIoinization = 0.5
 u:Ch/TOPASChemistry/BranchingRatios/RydbergStatesAndDiffuseBands/Relaxation = 0.5

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
 sv:Ch/TOPASChemistry/Reaction/SolvatedElectron/Oxygen/Products    = 1 "SuperoxideAnion"

 # Assign a reaction rate value:
 d:Ch/TOPASChemistry/Reaction/SolvatedElectron/Oxygen/ReactionRate = 1.9e10  /M/s

 # Assign the Reaction Type
 i:Ch/TOPASChemistry/Reaction/SolvatedElectron/Oxygen/ReactionRate = 2

 # SBS Compatible Reactions
 b:Ch/TOPASChemistry/Reaction/SolvatedElectron/Oxygen/CompatibleWithStepByStep = "True"

Reaction types can be 1, 2, 3, 4 or 5 depending on the reactants charge and mode of reaction (totally diffusion 
controlled  or partially diffusion  controlled) [Plante2017]_. Background reactions (type 6) are threated separetedly 
for either Step-By-Step or IRT simulations, as it will be discussed in a following section.

If the reaction does not produce any product or the product won't react further, for 
example,  the product from :sup:`•`\ OH + H\ :sup:`•` –> H :sub:`2`\ O, then the name ``None`` 
must be used, e.g::

 # Define the reaction without products:
 sv:Ch/TOPASChemistry/Reaction/Hydroxyl/Hydrogen/Products = 1 "None"

TOPAS-nBio provides a set of chemical parameters in the file ``TOPASChemistry.txt`` to be used with ``TsEmDNAChemistry``. 
These files (available in examples/processes) should be included in the usual way
with ``includeFile = "TOPASChemistry.txt"``. This set of reactions and reaction rates reproduces
experimental data from the literature, as reported in [RamosMendez2018]_. The example 
``ActiveChemistryRevised.txt`` shows how to define the new reaction capability.

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


Chemical Diffusion-Reaction Algorithms
--------------------------------------------
TOPAS-nBio incorporates two different methods for the diffusion and reaction of chemical species, the Step-By-Step (SBS) [Turner1983]_ 
and Independent Reaction Times (IRT) [Clifford1986]_ methods. Either method can be invoked by means of specific scorers. 
Main differences between both methods are the following:

 * SBS simulates the spatial and temporal evolution of the chemical species and thus is slower than IRT.
 * SBS allows to visualize chemical evolution.
 * IRT can reach up to two or three orders of magnitude shorter times than SBS.
 * Further chemical species can be defined only in IRT using parameters starting with ‘’Mo’’, see below.
 * Reactions between chemical species and background are defined in a different way between both methods.
 * Products from background reactions can be simulated only with IRT.

.. _Step-By-Step:

Step-By-Step
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
For the SBS method, TOPAS-nBio inherits the reactions and molecules from Geant4-DNA.
This is done automatically when using the default Geant4-DNA chemistry list ``g4em-dna-chemistry``.
When using the configurable chemistry list of TOPAS-nBio ``TsEmDNAChemistry``, the set of diffusion coefficients 
and reaction rates to be used can be found in the `GvalueRevisedPhysicsChemistry.txt`_ example.
To incorporate reactions with the background (scavenging) in TOPAS-nBio, the following structure must be used::

 # The Reactant: Scavenged Molecules
 sv:Sc/SBSGValue/Scavenger/Molecules = 2 "SolvatedElectron" "Hydroxyl"

 # The Scavenger concentrations
 dv:Sc/SBSGValue/Scavenger/Concentrations = 2 1e-2 1e-2 M

 # The Scavenger reaction rate
 dv:Sc/SBSGValue/Scavenger/ReactionRates = 2 5e10 2.7e9 /M/s

 # Confirm if there are products: Must be set to False.
 bv:Sc/SBSGValue/Scavenger/HasProducts = 2 "False" "False"

where M = 1 mol/dm3.

In the previous example solvated electrons and Hydroxyl radicals will be scavenged at a rate (scavenging capacity) of :math:`5 \times 10^{8} s^{-1}` for the solvated electrons and :math:`2.7 \times 10^{7} s^{-1}` for the hydroxyl radical, respectively.
Current version of TOPAS-nBio does not produces any product from background reaction. This capability will be added in a future release of TOPAS-nBio.

.. _Independent Reaction Times:

Independent Reaction Times
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
For IRT we provide a revisited reaction kinetics model (reaction and reaction rates) reported in [RamosMendez2021]_ which reconciliated simulated and measured 
G values at the picosecond range. This model is provided in the `TsIRTGvalue.txt`_ example.

The user can define new molecules by using the parameter system by using the ``Mo`` prefix as follows::

 # Define the internal Symbol of the Molecule
 s:Mo/DMSO/Symbol = "DMSO^0"

 # Define the Diffussion Coefficient
 d:Mo/DMSO/DiffusionCoefficient = 0 nm2/s
 
 # Define the Charge of the Molecule
 u:Mo/DMSO/Charge = 0
 
 # Define the reaction radius
 d:Mo/DMSO/Radius = 0.3 nm


New molecules defined with the previous method are not compatible with the StepByStep method. 
In IRT, the declaration of background reactions follows the same convention from the regular reactions::

 sv:Ch/TOPASChemistry/BackgroundReaction/hydroxyl/DMSO/Products = 1 "OHDMSOProduct^0"
 d:Ch/TOPASChemistry/BackgroundReaction/hydroxyl/DMSO/ReactionRate = 7.1e9 /M/s
 d:Ch/TOPASChemistry/BackgroundReaction/hydroxyl/DMSO/Concentration = 1e-1 M

Two models for simulating background reactions are provided, and can be selected as follows:

 # Either ExponentialSingleFactor or ExponentialDoubleFactor
 s:Ch/TOPASChemistry/BackgroundReaction/hydroxyl/DMSO/ScavengingModel = "ExponentialSingleFactor" 

Where ``ExponentialSingleFactor`` refers to method described in [Plante2017]_ and ``ExponentialDoubleFactor`` 
is the method described in [Pimblott1991]_.

TOPAS-nBio IRT allows for the activation and deactivation of reactions. This allows users to define a list of 
chemical reactions and pick and choose which ones of them will be active during the simulation without 
the need to introduce or delete the whole reaction::

 # Deactivate an already existing Chemical Reaction
 b:Ch/TOPASChemistry/Reaction/hydrogen/hydroxyl/Active = "False"

 # Deactivate an already existing Background Reaction
 b:Ch/TOPASChemistry/BackgroundReaction/hydroxyl/DMSO/Active = "False"

The section up to this point describes the ``pure`` IRT, which can be enabled as follows::
    
    s:Ch/TOPASChemistry/IRTProcedure = "pure"    

.. note:: the ``pure`` IRT is default in TOPAS-nBio and will be used if no other IRT procedure is specified.

In order to simulate the dose and dose rate dependency of long-term chemistry for continuous and quasi-continuous beam structures, 
the ``continuous`` IRT can be used instead. This IRT modality is described in detail in [Shin2025]_::

    s:Ch/TOPASChemistry/IRTProcedure = "continuous" 

Simulations considering pH
---------------------------------------
The IRT method of TOPAS-nBio allows the user to scale the pH value of the medium. This can be usefull to model the physical conditions of experimental setups in more detail. The pH scalling was developed following the work of [Autsavapromporn2007]_ and [Plante2011]_. To change the pH of the medium the following parameters are used::

 # Modeling acid properties can be set to "H2SO4" or "Generic"
 # "Generic" is an artifical PH scalling in which no acid is present
 s:Ch/TOPASChemistry/ModelAcidPropertiesFromSubstance = "H2SO4"

 # Use acid concentration to change pH, not available with "Generic" solvent
 s:Ch/TOPASChemistry/ModelAcidPropertiesWithConcentration = 0.4 M

 # Use a specific pH and calculate acid concentration
 s:Ch/TOPASChemistry/ModelAcidPropertiesWithpH = 4.6

The last two parameters; ``ModelAcidPropertiesWithConcentration`` and ``ModelAcidPropertiesWithpH`` canot be 
used at the same time, if the user attemps to do so TOPAS will exit and an error message displayed. TOPAS-nBio will 
automatically scale reaction rates for reactions between two charged chemical species based on the ionic strength 
of the medium. An example is provide shown this capability throught the Fricke dosimeter, see example TOPAS-nBio/example/scorers/FrickeIRT


IRT Automatic Temperature Scalling
----------------------------------
TOPAS-nBio provides its users with a built-in temperature scalling algorithm compatible with IRT. 
It is based on the work of [Elliot1994]_ and [DuPenhoat200]_.
Specif details regarding the implementation can be found in [RamosMendez2022]_. To use the temperature scaling of reaction rates and
diffusion coefficients, the users must the parameter ``u:Ch/TOPASChemistry/Temperature`` and setting it to the temperature in Celsius.

For the best results, it is recommended to also change the water density to the value that corresponds with the desired temperature.
We provide users with two examples that showcase this TOPAS-nBio IRT feature in the folder ``/examples/scorers/Temperatures``: ``TemperatureExample_20C.txt`` and ``TemperatureExample_90C.txt``.

We remind users that this specific implementation is only valid between 0 and 90 degrees Celsius.


References
-----------

.. [Karamitros2012]  Karamitros M, Mantero A, Incerti S, Friedland W, Baldacchino G, Barberet P, 
                     Bernal M, Capra R, Champion C, El Bitar Z, Francis Z, Gueye P, Ivanchenko A, 
                     Ivanchenko V, Kurashige H, Mascialino B, Moretto P, Nieminen P, Santin G, 
                     Seznec H, Tran H N, Villagrasa C and Zacharatou C 2011 Modeling Radiation 
                     Chemistry in the Geant4 Toolkit Prog. Nucl. Sci. Technol. 2 503–8 
                     http://www.aesj.or.jp/publication/pnst002/data/503-508.pdf
.. [Incerti2016]  Incerti S, Douglass M, Penfold S, Guatelli S and Bezak E 2016 
                     Review of Geant4-DNA applications for micro and nanoscale simulations Phys. 
                     Medica 32 1187–200 http://www.physicamedica.com/article/S1120-1797(16)30927-9/pdf
.. [RamosMendez2018] Ramos-Méndez J, Perl J, Schuemann J, McNamara A, Paganetti H and Faddegon B 
                     2018 Monte Carlo simulation of chemistry following radiolysis with TOPAS-nBio 
                     Phys. Med. Biol. 63 105014 http://iopscience.iop.org/article/10.1088/1361-6560/aac04c
.. [RamosMendez2021] Ramos-Méndez J, LaVerne J, D-Kondo J, et. al. 2021
                     TOPAS-nBio validation for simulating water radiolysis and DNA damage under low-LET irradiation. 
                     Phys. Med. Biol. https://iopscience.iop.org/article/10.1088/1361-6560/ac1f39
.. [RamosMendez2022] Ramos-Méndez, J., García-García, O., Domínguez-Kondo, J., Laverne, J. A., Schuemann, J., Moreno-Barbosa, E.,
                     Faddegon, B. (2022). TOPAS-nBio simulation of temperature-dependent indirect DNA strand break yields. Physics in
                     Medicine and Biology. https://doi.org/10.1088/1361-6560/ac79f9
.. [Clifford1986]    Clifford P, Green N J B, Oldfield M J, Pilling M J and Pimblott S M 1986 
                     Stochastic Models of Multi-species Kinetics in Radiation-induced Spurs J. Chem. Soc., Faraday Trans. 1 82 2673–89 http://doi.org/10.1039/F19868202673
.. [Turner1983]      Turner JE, Magee JL, Wright HA, Chatterjee A, Hamm RN, RitchieRH 1983 
                     Physical and chemical development of electron tracksin liquid water. Radiat Res 96:437–449 https://www.jstor.org/stable/3576111
.. [Plante2017]      Plante I and Devroye L 2017 Considerations for the independent reaction times and step-by-step 
                     methods for radiation chemistry simulations" Radiat. Phys. Chem. 139 157-172 http://dx.doi.org/10.1016/j.radphyschem.2017.03.021
.. [Pimblott1991]    Pimblott SM, Pilling MJ, and Green NJB 1991
                     Stochastic Models of Sput Kinetics In Water. Radiat. Phys. Chem. 37 (3) 377-388 
                     https://doi.org/10.1016/1359-0197(91)90006-N
.. [Schuemann2019a]   Schuemann, J, McNamara, A L, Ramos-Méndez, J, Perl, J, Held, K D, Paganetti, H, Incerti, S, 
                     Faddegon, B 2019 TOPAS-nBio: An Extension to the TOPAS Simulation Toolkit for Cellular and 
                     Sub-cellular Radiobiology Radiation Research, 191(2), 125–138 https://pubmed.ncbi.nlm.nih.gov/30609382/

.. [Autsavapromporn2007] Autsavapromporn N, Meesungnoen J, Plante I, Jay-Gerin J-P 2007. 
                         Monte Carlo simulation study of the effects of acidity and LET on the primary free-radical and molecular yields of water radiolysis — Application to the Fricke dosimeter. Canadian Journal of Chemistry, 85(3), 214–229. https://doi.org/10.1139/v07-021

.. [Plante2011] Plante I 2011. A Monte-Carlo step-by-step simulation code of the non-homogeneous chemistry of the radiolysis 
                of water and aqueous solutions-Part II: Calculation of radiolytic yields under different conditions of LET, pH, and temperature. Radiation and Environmental Biophysics, 50(3), 405–415. https://doi.org/10.1007/s00411-011-0368-7

.. [Elliot1994] Elliot A J 1994. Rate Constants and G-Values for the Simulation of the Radiolysis of Light Water over the Range 0-300°C. https://inis.iaea.org/records/1964r-srn97

.. [DuPenhoat200] Du Penhoat M-A H, Goulet T, Frongillo Y, Fraser M J, Bernat P, Jay-Gerin J P 2000. 
                  Radiolysis of liquid water at temperatures up to 300 °c: A Monte Carlo simulation study. Journal of Physical Chemistry A, 104(50), 11757–11770. https://doi.org/10.1021/jp001662d

.. [Shin2025] Wook-Geun Shin, J Naoki D-Kondo, José Ramos-Méndez, Jay A LaVerne, Bethany Rothwell, Alejandro Bertolet, Aimee McNamara, Bruce Faddegon, Harald Paganetti and Jan Schuemann 2025.
                Investigation of hydrogen peroxide yields and oxygen consumption in high dose rate irradiation: a TOPAS-nBio Monte Carlo study. Phys. Med. Biol. 70 (2025) 015012. https://iopscience.iop.org/article/10.1088/1361-6560/ad9ce2

.. _GvalueRevisedPhysicsChemistry.txt: https://github.com/topas-nbio/TOPAS-nBio/blob/master/examples/scorers/SBSGetGValue/GvalueRevisedPhysicsChemistry.txt

.. _TsIRTGvalue.txt: https://github.com/topas-nbio/TOPAS-nBio/blob/e8e23670d48871715f89635aabcf1be43d3ba27c/examples/scorers/IRTGetGValue/TsIRTGvalue.txt