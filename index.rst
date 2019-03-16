.. TOPAS-nBio documentation master file, created by
   sphinx-quickstart on Thu Apr 12 01:24:56 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.


.. note::
    This page will be fully avaialable (hopefully) by 04/01/2019.


Welcome to the documentation of TOPAS-nBio!
=========================================================================================

The TOPAS Monte Carlo (MC) system is used in radiation therapy and medical imaging research, having played a significant role in making Monte Carlo simulations widely available for proton therapy related research. While TOPAS provides detailed simulations of patient scale properties, the fundamental unit of the biological response to radiation is a cell. Thus, our goal was to develop TOPAS-nBio, an extension of TOPAS dedicated to advance understanding of radiobiological effects at the (sub-)cellular (i.e., the cellular and sub-cellular) scale. TOPAS-nBio was designed as a set of open source classes that extends TOPAS to model radiobiological experiments. TOPAS-nBio is based on and extends Geant4-DNA, which extends the Geant4 toolkit, the basis of TOPAS, to include very low-energy interactions of particles down to vibrational energies. TOPAS-nBio explicitly simulates every particle interaction (i.e., without using condensed histories) and propagates radiolysis products. To further facilitate the use of TOPAS-nBio, a graphical user interface was developed. TOPAS-nBio offers full track-structure Monte Carlo simulations, integration of chemical reactions within the first millisecond, an extensive catalogue of specialized cell geometries as well as sub-cellular structures such as DNA and mitochondria, and interfaces to mechanistic models of DNA repair kinetics. We compared TOPAS-nBio simulations to measured and published data of energy deposition patterns and chemical reaction rates (G values). Our simulations agree well within the experimental uncertainties. Additionally, we expanded the chemical reactions and species provided in Geant4-DNA and developed a new method based on independent reaction times (IRT), including a total of 72 reactions classified into 6 types between neutral and charged species. Chemical stage simulations using IRT were a factor of 145 faster than with step-by-step tracking. The IRT method is, however, still under development and will only be part of a later release.


In summary, the TOPAS-nBio extension to the TOPAS MC application offers access to accurate and detailed multiscale simulations, from a macroscopic description of the radiation field to microscopic description of biological outcome for selected cells. TOPAS-nBio offers detailed physics and chemistry simulations of radiobiological experiments on cells simulating the initially induced damage and links to models of DNA repair kinetics. By releasing TOPAS-nBio as an open source extension to TOPAS, we intend to encourage active involvement of the research community to continuously extend and improve the features of TOPAS-nBio.

|
|

**TOPAS-nBio is described in the following publications:**

**Main Publication**
When using TOPAS-nBio, please cite this paper:

Schuemann, J., McNamara, A. L., Ramos-Méndez, J., Perl, J., Held, K. D., Paganetti, H., Incerti, S., Faddegon, B. (2019). TOPAS-nBio: An Extension to the TOPAS Simulation Toolkit for Cellular and Sub-cellular Radiobiology. Radiation Research, 191(2), 125–138. http://doi.org/10.1667/RR15226.1

|

**The Geant4-DNA project**
TOPAS-nBio is based on and extends Geant4-DNA. Information on the Geant4-DNA project can be found at:

http://geant4-dna.org/

|

**Other relevant publications**

McNamara, A. L., Ramos-Méndez, J., Perl, J., Held, K., Dominguez, N., Moreno, E., Henthorn, N., Kirkby, K. J., Meylan, S., Villagrasa, C., Incerti, S., Faddegon, B., Paganetti, H., Schuemann, J. (2018). Geometrical structures for radiation biology research as implemented in the TOPAS-nBio toolkit. Physics in Medicine and Biology, 63(17), 175018. http://doi.org/10.1088/1361-6560/aad8eb

Ramos-Méndez, J., Perl, J., Schuemann, J., McNamara, A., Paganetti, H., & Faddegon, B. (2018). Monte Carlo simulation of chemistry following radiolysis with TOPAS-nBio. Physics in Medicine and Biology, 63(10), 105014. http://doi.org/10.1088/1361-6560/aac04c

Ramos-Méndez, J., Schuemann, J., Incerti, S., Paganetti, H., Schulte, R., & Faddegon, B. (2017). Flagged uniform particle splitting for variance reduction in proton and carbon ion track-structure simulations. Physics in Medicine and Biology, 62(15), 5908–5925. http://doi.org/10.1088/1361-6560/aa7831

McNamara, A., Geng, C., Turner, R., Méndez, J. R., Perl, J., Held, K., et al. (2017). Validation of the radiobiology toolkit TOPAS-nBio in simple DNA geometries. Physica Medica : PM : an International Journal Devoted to the Applications of Physics to Medicine and Biology : Official Journal of the Italian Association of Biomedical Physics (AIFB), 33, 207–215. http://doi.org/10.1016/j.ejmp.2016.12.010

Underwood, T. S. A., Sung, W., McFadden, C. H., McMahon, S. J., Hall, D. C., McNamara, A. L., et al. (2017). Comparing stochastic proton interactions simulated using TOPAS-nBio to experimental data from fluorescent nuclear track detectors. Physics in Medicine and Biology, 62(8), 3237–3249. http://doi.org/10.1088/1361-6560/aa6429

|

**Original TOPAS paper**

Perl, J., Shin, J., Schuemann, J., Faddegon, B., & Paganetti, H. (2012). TOPAS: an innovative proton Monte Carlo platform for research and clinical applications. Medical Physics, 39(11), 6818–6837. http://doi.org/10.1118/1.4758060

|

Getting Started
^^^^^^^^^^^^^^^^^^
.. toctree::
    :maxdepth: 1

    docs/Introduction
    docs/HowToInstall
    docs/Geometries
    docs/Chemistry
    docs/BiologyModeling
    

The TOPAS-nBio Collaboration
^^^^^^^^^^^^^^^^^^^^^^^
.. toctree::
    :maxdepth: 1

    docs/Members

Indices and tables
^^^^^^^^^^^^^^^^^^^^^

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`


Other test section
^^^^^^^^^^^^^^^^^^^^^
