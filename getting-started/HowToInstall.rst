How to Install TOPAS-nBio
=========================

Installing OpenTOPAS
--------------------

TOPAS-nBio is an extension to the TOPAS toolkit, using the OpenTOPAS version. In order to install TOPAS-nBio, users must first install OpenTOPAS. 

TOPAS-nBio (starting with version 3.0) requires OpenTOPAS version 4.0 or newer. 

OpenTOPAS can be downloaded from `OpenTOPAS <https://opentopas.github.io/index.html>`_. Installation instructions for OpenTOPAS can be found on the OpenTOPAS GitHub in the "quickStart" files.


Installing TOPAS-nBio
----------------------
Download the TOPAS-nBio extension and put in a separate directory (e.g. /PathToWorkDirectory/TOPAS-nBio). This path should not be inside the main TOPAS installation directory to avoid accidental loss of your extensions and parameter files when updating to a new TOPAS version. 
After building TOPAS link the TOPAS-nBio extension and rebuild the code::
 
	cmake -DTOPAS_EXTENSIONS_DIR=/PathToWorkDirectory/TOPAS-nBio
	make -j8 
