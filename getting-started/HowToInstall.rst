How to Install TOPAS-nBio
=========================

Installing TOPAS
-----------------

TOPAS-nBio is an extension to the TOPAS toolkit. In order to install TOPAS-nBio, users must first install TOPAS. 

TOPAS-nBio requires TOPAS version 3.2.0 or newer. 

TOPAS can be downloaded from `topasmc.org <http://topasmc.org>`_. Installation instructions for TOPAS can be found bundled the following README.txt: 

.. highlight:: plain
.. literalinclude:: README.txt

An updated README.txt file is distributed with every new TOPAS version.


Installing TOPAS-nBio
----------------------
Download the TOPAS-nBio extension and put in a separate directory (e.g. /PathToWorkDirectory/TOPAS-nBio). This path should not be inside the main TOPAS installation directory to avoid accidental loss of your extensions and parameter files when updating to a new TOPAS version. 
After building TOPAS link the TOPAS-nBio extension and rebuild the code::
 
	cmake -DTOPAS_EXTENSIONS_DIR=/PathToWorkDirectory/TOPAS-nBio
	make -j8 
