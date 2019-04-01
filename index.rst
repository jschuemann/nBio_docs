.. TOPAS-nBio documentation master file, created by
   sphinx-quickstart on Thu Apr 12 01:24:56 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.


.. note::
    This page is currently under construction. 
    TOPAS-nBio is expected to be released this month after the next TOPAS release which includes essential updates required for TOPAS-nBio.


Welcome to the documentation of TOPAS-nBio!
=========================================================================================

The TOPAS Monte Carlo (MC) system is used in radiation therapy and medical imaging research, having played a significant role in making Monte Carlo simulations widely available for proton therapy related research. While TOPAS provides detailed simulations of patient scale properties, the fundamental unit of the biological response to radiation is a cell. Thus, our goal was to develop TOPAS-nBio, an extension of TOPAS dedicated to advance understanding of radiobiological effects at the (sub-)cellular (i.e., the cellular and sub-cellular) scale. TOPAS-nBio was designed as a set of open source classes that extends TOPAS to model radiobiological experiments. TOPAS-nBio is based on and extends Geant4-DNA, which extends the Geant4 toolkit, the basis of TOPAS, to include very low-energy interactions of particles down to vibrational energies. TOPAS-nBio explicitly simulates every particle interaction (i.e., without using condensed histories) and propagates radiolysis products. To further facilitate the use of TOPAS-nBio, a graphical user interface was developed. TOPAS-nBio offers full track-structure Monte Carlo simulations, integration of chemical reactions within the first millisecond, an extensive catalogue of specialized cell geometries as well as sub-cellular structures such as DNA and mitochondria, and interfaces to mechanistic models of DNA repair kinetics. 

The TOPAS-nBio extension to the TOPAS MC application offers access to accurate and detailed multiscale simulations, from a macroscopic description of the radiation field to microscopic description of biological outcome for selected cells. TOPAS-nBio offers detailed physics and chemistry simulations of radiobiological experiments on cells simulating the initially induced damage and links to models of DNA repair kinetics. By releasing TOPAS-nBio as an open source extension to TOPAS, we intend to encourage active involvement of the research community to continuously extend and improve the features of TOPAS-nBio.


.. toctree::
    :maxdepth: 1
    :caption: Getting Started

    getting-started/Introduction
    getting-started/HowToInstall
    getting-started/ParameterFile
    getting-started/Members
    getting-started/Citations
   
    
.. toctree::
    :maxdepth: 1
    :caption: Geometries

    Geometries/Intro
    Geometries/Cells
    Geometries/DNAmodels
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
  
