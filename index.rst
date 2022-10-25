.. TOPAS-nBio documentation master file, created by
   sphinx-quickstart on Thu Apr 12 01:24:56 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.


.. note:: 
    New Release, v2.0 (Oct. 2022)!

.. note:: 
    TOPAS-nBio is now released as version v2.0! 

    TOPAS-nBio currently works with TOPAS release 3.8 and newer release. 
    
    Important updates:
        Geometry:
            - New geometries added
            - DNA has been updated
            - Hi-C geometry for nucleus available
            - new cell + nanoparticle + organelles provided by Marc Hahn
        Scoring:
            - New scoring system
            - New method to score indirect damage to bases
        Chemistry:
            - IRT has been included
            - Additional scorers for G-values were added
        Biology:
            - DaMaRiS has been integrated.

        In addition, this release includes several fixes and improvements.

    The code is available at:
    https://github.com/topas-nbio/TOPAS-nBio/releases/tag/v1.0
    
    Please use the code as much as possible and report any issues to the TOPAS-nBio forum:
    https://groups.google.com/forum/#!forum/topas-nbio-users
    
  

Welcome to the documentation of TOPAS-nBio!
=========================================================================================

The TOPAS Monte Carlo (MC) system has played a significant role in making Monte Carlo simulations widely available for proton therapy related research and is further used in radiation therapy and medical imaging research. While TOPAS provides detailed simulations of patient scale properties, the fundamental unit of the biological response to radiation is a cell. Thus, we developed TOPAS-nBio, an extension of TOPAS dedicated to advance understanding of radiobiological effects at the (sub-)cellular (i.e., the cellular and sub-cellular) scale. TOPAS-nBio is designed as a set of open source classes that extends TOPAS to model radiobiological experiments. TOPAS-nBio is based on and extends Geant4-DNA, which extends the Geant4 toolkit, the basis of TOPAS, to include very low-energy interactions of particles down to vibrational energies. TOPAS-nBio explicitly simulates every particle interaction (i.e., without using condensed histories) and propagates radiolysis products. To further facilitate the use of TOPAS-nBio, a graphical user interface was developed. TOPAS-nBio offers full track-structure Monte Carlo simulations, integration of chemical reactions within the first millisecond, an extensive catalogue of specialized cell geometries as well as sub-cellular structures such as DNA and mitochondria. TOPAS-nBio provides the initially induced damage patterns and interfaces to mechanistic models of DNA repair kinetics. Thus, together with TOPAS, this extension offers access to accurate and detailed multiscale simulations, from a macroscopic description of the radiation field to microscopic description of biological outcome.

The TOPAS-nBio package was described in Radiation Research, 2019, 191(2), p.125 (also see :ref:`Citations`).
By releasing TOPAS-nBio as an open source extension to TOPAS, we intend to encourage active involvement of the research community to continuously extend and improve the features of TOPAS-nBio. 


.. toctree::
    :maxdepth: 1
    :caption: Getting Started

    getting-started/Introduction
    getting-started/HowToInstall
    getting-started/ParameterFile
    getting-started/Members
    getting-started/Citations
    getting-started/License
   
    
.. toctree::
    :maxdepth: 1
    :caption: Geometries

    Geometries/Intro
    Geometries/Cells
    Geometries/DNAmodels
    Geometries/Nucleus
    Geometries/Other	
    

.. toctree::
    :maxdepth: 1
    :caption: Processes
 
    Processes/Intro		
    Processes/Physics
    Processes/Chemistry


.. toctree::
    :maxdepth: 1
    :caption: Biological Modeling
	
    Modeling/Intro
    Modeling/DNARepair

.. toctree::
    :maxdepth: 1
    :caption: GUI
	
    GUI/Intro	

.. toctree::
    :maxdepth: 1
    :caption: Scorers

    Scorers/Intro
    Scorers/Cells
    Scorers/DNA
    Scorers/Nucleus
    Scorers/Chem
    
  
