Chemical Scorers
================
Chemical scorers are those that utilize either the :ref:`Step-By-Step` or the :ref:`Independent Reaction Times` 
algorithms to simulate the diffusion and reaction of chemical species. TOPAS-nBio provides six chemical scorers, 
two for SBS and four for IRT. These scorers are:

+-------------+-------------------------+
| Scorer Type | Scorer Quantity         |
+=============+=========================+
|   SBS       | TsSBSGValue             |
+             +-------------------------+
|             | TsSBSMoleculesAtTime    |
+-------------+-------------------------+
|   IRT       | TsIRTGvalue             |
+             +-------------------------+
|             | TsIRTInterTrack         |
+             +-------------------------+
|             | TsIRTInterPulse         |
+             +-------------------------+
|             | TsIRTStrandBreaks       |
+-------------+-------------------------+

Chemical scorers provide a user a way to terminate the tracks based on total primary energy loss, total primary track length or 
number of primary particle steps. This is implemented as both, a way to control the particle track size 
for more control of the G-values and as a way to reduce the total number of chemical species produced by a certain particle track.

Step-By-Step Scorers
--------------------

SBS GValues
~~~~~~~~~~~
The Step-By-Step G-Values scorer. It was the first chemical scorer, available since the first release of 
TOPAS-nBio. Overall, It remained the same from its past version, however the naming convention was changed 
in order to fall in line with the rest of the chemical scorers. The TOPAS-nBio parameter name for this scorer 
component changed from ``GValue`` to ``TsSBSGValue``. An example of the usage of this scorer can be found at
``/examples/scorers/TsSBSGValues/`` with TOPAS-nBio defined reactions ``GvalueRevisedPhysicsChemistry.txt`` and 
Geant4-DNA default reactions ``GvalueG4DNADefault.txt``. The main parameter to setup this scorer is::

 # The times at which to report the GValues
 dv:Sc/SSBGvalue/TimeToRecord = 7 1 10 100 1e3 1e4 1e5 1e6 ps

Alongside this parameter, the scorer also needs the following particle track kill options::

 # Total Energy Loss to Kill the primary track
 # Preferably some percentage of total energy
 d:Sc/SSBGvalue/KillPrimaryIfEnergyLossExceeds = 10 eV

 # If total energy loss by primary exceeds this value the track will be aborted
 # And no chemistry will be conducted
 d:Sc/SSBGvalue/AbortEventIfPrimaryEnergyLossExceeds = 100 eV

 # Total track length to kill the primary track
 d:Sc/SSBGvalue/KillPrimaryBasedOnTrackLength = 1 um

The rest of the parameters will be handled by the ``Ch`` chemistry parameters. Most of the chemical scorers 
share these same particle track kill options. This scorer has been used to calculate G-values with relatively 
good agreement as published in [Ramos-Méndez2018]_.


Number of Molecules At Time
~~~~~~~~~~~~~~~~~~~~~~~~~~~
The ``TsSBSMoleculesAtTime`` scorer is a way to obtain the total number of a chemical specie instead of 
its G-value (species per 100 eV of energy deposit). It shares the same particle track kill options from the 
``TsSBSGValue`` scorer. An example of such scorer can be found in the folder ``examples/scorers/TsSBSMoleculesAtTime/``, 
with the file name ``TsSpeciesAtTime.txt``. Instead of providing the explicit 
time values to report G-values from the ``TsSBSGValue`` scorer, an automatic binning is provided by using 
the following four parameters::

 # Number of time bins
 d:Sc/MoleculesAtTime/TimeBins = 100

 # The lower time limit
 d:Sc/MoleculesAtTime/TimeBinMin = 1 ps

 # The upper time limit
 d:Sc/MoleculesAtTime/TimeBinMax = 1e6 ps

 # True for logarithmic bins, false for lineal
 b:Sc/MoleculesAtTime/TimeBinLog = "True"

Independent Reaction Times Scorers
----------------------------------

IRT GValues
~~~~~~~~~~~
The ``TsIRTGValue`` scorer is the IRT version of ``TsSBSGValue``. The necessary parameters to setup the 
scorer are similar to those from the ``TsSBSMoleculesAtTime`` scorer. Alongside the particle track kill 
options the necesary parameters are::

 # Scorer Cutoff time
 d:Sc/IRTGValue/CutoffTime = 1 us

 # Optional: if it exists the regular track kill options are ignored: Defaults to 0
 i:Sc/IRTGValue/MaximumNumberOfSteps = 1e6

 # Report DeltaG Values: Defaults to false
 b:Sc/IRTGValue/ReportDeltaGValues = "True"

DeltaG values are an optional part of this scorer, they report the number of times a certain reaction 
is performed during the simulation per 100 eV of energy deposit (same units as the G-values). The output 
file of the DeltaG values is goin to have the same name from the ``OutputFile`` parameter from the scorer with a ``_DeltaG.phsp`` at the end.

Apart from this parameters, all IRT scorers take additional parameters from the ``/procesess/TsIRT.cc`` 
file in order to create the time bins to report the G-values::

 # The lower bin limit
 d:Sc/IRTGValue/TimeLower = 1 ps

 # The upper bin limit
 d:Sc/IRTGValue/TimeUpper = 1 us

 # The number of Logarithmic bins
 i:Sc/IRTGValue/TimeBins = 100

 # Use High Time Scavenger: Defaults to False
 b:Sc/IRTGValue/HighTimeScavenger = "False"

The ``TimeLower``, ``TimeUpper`` and ``TimeBins`` are optional and defaults to the same values shown here. 
The ``HighTimeScavenger`` parameter is used to limit the scavenging reaction to 1 micro second, this is done 
in order to avoid long simulation times between cycling reactions of the form::

 ReactantA + ScavengerA = ReactantB
 ReactantB + ScavengerB = ReactantA

By having this limiter, those reactions won’t be performed after 1 micro second. However some applications may 
depend on higher simulations times with the need of special scavengers like the Fricke dosimeter in which case 
users can opt to remove this limiter in exchange of simulation time. 

An example of the usage of this scorer can be found at ``/examples/scorers/TsIRTGvalue/TsIRTGvalue.txt``. TOPAS-nBio 
IRT simulations accuracy has been most recently reported in [Ramos-Méndez2021]_. The Fricke dosimeter has been 
implemented alongside a way to automatically change reaction times by pH as explained in :ref:`Independent Reaction Times`, 
the ``/examples/scorers/TsFricke/FrickeIRT.txt`` example shows the implementation of the results published in [Ramos-Méndez2020]_.


IRT GValues Inter-Track
~~~~~~~~~~~~~~~~~~~~~~~
TOPAS-nBio allows users to quantify the effect of multiple particles track on the chemical yields, in what is known 
as inter track effects. These effects don't appear unless two or more tracks happen to interact with the medium very 
closely considering both the space and time of ionization and excitation of the medium. Specifically speaking, 
two particle tracks must happen with a time separation of at least 1 micro second and a separation of a few tens 
of nano meters apart.

The ``TsIRTInterTrack`` scorer was made to quantify this effect in a track-by-track basis. It allows the use of 
as many tracks as the user specifies distributed in the space and time which the user can manually specify with::

 # Set to True to allow multiple tracks per IRT run: Defaults to False
 b:Sc/IRTGValuesIT/UseMultipleTracks = "True"

 # Set the individual time at which each track hits the scorer
 dv:Sc/IRTGValuesIT/TimeDelay = 5 1 2 2 5 1e3 ps

 # Set the spatial offset of each track per IRT run.
 dv:Sc/IRTGValuesIT/SpatialOffsetX = 0 1 0  5 10 nm
 dv:Sc/IRTGValuesIT/SpatialOffsetY = 0 1 1 50 20 nm
 dv:Sc/IRTGValuesIT/SpatialOffsetZ = 0 1 1  0 10 nm

An example of this scorer can be found at ``/examples/scorers/TsInterTrack/TsIRTInterTrack.txt``. Using this scorer
it is possible to recreate the results reported in [Kreipl2009]_.

IRT GValues Inter-Pulse
~~~~~~~~~~~~~~~~~~~~~~~
Experimentally, a track-by-track inter-track effect is unlikely to be conducted, instead we rely in the use of a pulse irradiation.
By using pulse irradiation, high radiation dose rates can be achieved in what is called FLASH irradiation. 
TOPAS-nBio includes a scorer that allows such uses named ``TsIRTInterPulse``. This scorer was used in the published paper 
[Ramos-Méndez2020]_. It completely removes the particle track kill options while providing additional options for the 
pulse distributions with the parameters::

 # Total Dose used per IRT run
 d:Sc/IRTCummulative/PrescribedDose = 5 Gy

 # Pulse Distribution it can be: 
 # "Gaussian", "Uniform", "None", "Discrete" and "Exponential"
 s:Sc/IRTCummulative/PulseDistribution = "Gaussian"

 # Pulses to use, dose will be distributed among them: Defaults to 1
 i:Sc/IRTCummulative/NumberOfPulses = 5

 # Number of Pulses per unit time
 d:Sc/IRTCummulative/PulsesFrequency = 1e6 /s

If the pulses are "Gaussian" or "Uniform" the following parameters must be set::

 # The mean time of the pulse
 d:Sc/IRTCummulative/PulseTimeMean = 1 us

 # The average duration of the pulse by using the FWHM
 d:Sc/IRTCummulative/PulseFWHM = 2 us

If the pulses are "Discrete", the following parameters are needed::

 dv:Sc/IRTCummulative/TimeValues  = 5   1  1.5    2  2.5    5 us 
 uv:Sc/IRTCummulative/TimeWeights = 5 0.1 0.25 0.05 0.25 0.35 

If the pulses are "Exponential" only the mean time of the pulse is needed with::

 d:Sc/IRTCummulative/PulseTimeMean = 1 us

Chemical simulations always start at 1 pico second, taking into account the physical and pre-chemical stages. 
Due to the behaviour the time sampling when using pulse distributions it is possible to have chemical species 
created and diffusing before the chemical stage. To avoid this, users can use the following parameter::

 b:Sc/IRTCummulative/ForceLowTimeCutTo1ps = True

The default value of such parameter is ``False``.

TOPAS-nBio provides an example of this scorer in ``/examples/scorers/TsIRTCummulative/TsIRTCummulative.txt``. 
The effect of FLASH radiation on chemical yields using this scorer in TOPAS-nBio has been published [Ramos-Méndez2020]_, 
the user can check such reference for more information regarding the expected results and details about FLASH.


The user must take care to not overload the IRT of TOPAS-nBio by sending either a high number of pulses or a high dose. 
The use of this scorer may consume all the memory available in a computer causing it to freeze or crash, user discretion is advised.


IRT DNA Strand Breaks
~~~~~~~~~~~~~~~~~~~~~
Both the SBS and IRT scorers can be used for DNA strand breaks simulations. The specific implementation of such simulations 
can be very complex depending on the reaction-diffusion algorithm. The TOPAS-nBio code provides a way to simulate DNA strand 
breaks in plasmid DNA using the IRT method. In order to properly run plasmid DNA strand breaks simulations, the user must do 
some pre-simulation setup either by hand or by using the :ref:`IRT Supercoiled Plasmid Setup` provided in the TOPAS-nBio code. 
The ``TsIRTStrandBreaks`` scorer assumes the user has already done the necessary steps and has the necessary files to properly 
score de DNA strand breaks by using:: 

 d:Sc/DNAStrandBreak/PrescribedDose = 50 Gy
 s:Sc/DNAStrandBreak/InputFile = "pUC19_Plasmids.xyz"
 iv:Sc/DNAStrandBreak/OnlyUseDNAMoleculesWithID = 1 104

Where the ``InputFile`` refers to the file containing the position of the DNA molecules. ``OnlyUseDNAMoleculesWithID`` 
allows to filter DNA molecules prior to entering the IRT and save time. An example of this scorer can be found at 
``/examples/scorers/TsScoreDNADamage/PlasmidIrradiation/TsScoreDNADamage.txt``. For more information regarding the 
performance and setup of this example can be found in [Ramos-Méndez2021]_.

This scorer will produce three different files, a ``phsp`` file with the number of molecules at a given time of the current simulation, 
a header file for such file and finally a ``.dnadamage`` file. The ``.dnadamage`` file contains the ``Base Pair ID`` and ``Strand ID`` of 
each break. It is up to the user to work around this information to obtain the number of Single Strand Breaks and Double Strand Breaks. 
The ``Base Pair ID`` will be a number between 1 and the number of plasmids multiplied by the number of base pair per plasmid. 
The user will need to obtain the ``Plasmid ID`` by dividing the ``Base Pair ID`` by the number of base pairs of the specific 
plasmid used.

It is important to mention that this scorer is not ``Multithread`` compatible and must be used with a thread number of 1. 
Future releases may address this limitation.


References
----------

.. [Ramos-Méndez2018] Ramos-Méndez J, Perl J, Schuemann J, McNamara A, Paganetti H, Faddegon B 2018. 
                  Monte Carlo simulation of chemistry following radiolysis with TOPAS-nBio. Physics in Medicine and Biology,
                  63(10), 0–12. https://doi.org/10.1088/1361-6560/aac04c

.. [Kreipl2009] Kreipl M S, Friedland W, Paretzke H G 2009. Time- and space-resolved Monte Carlo study of water radiolysis 
                for photon, electron and ion irradiation. Radiation and Environmental Biophysics, 48(1), 11–20. 
                https://doi.org/10.1007/s00411-008-0194-8

.. [Ramos-Méndez2020] Ramos-Méndez J, Domínguez-Kondo N, Schuemann J, McNamara A, Moreno-Barbosa E, Faddegon B 2020. 
                     LET-dependent intertrack yields in proton irradiation at ultra-high dose rates relevant for FLASH therapy. 
                     Radiation Research, 194(4), 351–362. https://doi.org/10.1667/RADE-20-00084.1

.. [Ramos-Méndez2021] Ramos-Mendez J A, LaVerne J A, Domínguez-Kondo J N, Milligan J, Stepan V, Stefanová K, Perrot Y, 
                      Villagrasa C, Shin W-G, Incerti S, McNamara A L, Paganetti H, Perl J, Schuemann J, Faddegon B 2021. 
                      TOPAS-nBio validation for simulating water radiolysis and DNA damage under low-LET irradiation. 
                      Physics in Medicine; Biology, 66(June), 1–12. https://doi.org/10.1088/1361-6560/ac1f39