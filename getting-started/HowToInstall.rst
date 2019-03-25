How to Install TOPAS-nBio
=========================

Installing TOPAS
-----------------

TOPAS-nBio is an extension to the TOPAS toolkit. In order to install TOPAS-nBio, users must first install TOPAS. 

TOPAS can be downloaded from `topasmc.org <http://topasmc.org>`_. Installation instructions for TOPAS can be found bundled the following README.txt: 


.. highlight:: plain
.. literalinclude:: README.txt


Installing TOPAS-nBio
----------------------
Download the TOPAS-nBio extension and put in a separate directory (e.g. /Applications/TOPAS-nBio). After building TOPAS link the TOPAS-nBio extension and rebuild the code::
 
	cmake -DTOPAS_EXTENSIONS_DIR=/Applications/TOPAS-nBio
	make -j8 
