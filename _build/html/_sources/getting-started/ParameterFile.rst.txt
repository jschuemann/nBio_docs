Parameter File System
======================

TOPAS-nBio is an extension to OpenTOPAS, which follows a consistent set of design paradigms. OpenTOPAS (from here on simply TOPAS) is controlled by a simple text file which is used to specify the all the simulation parameters.  

Detailed documentation is provided on the `TOPAS Parameter System`_. Here we provide a brief summary of the parameter syntax.

The system takes a set of "Parameters Files," simple text files made up of lines of key/value pairs::

    Parameter_Type : Parameter_Name = Parameter_Value # Optional comment


The order of lines within a parameter file does not matter.

A Parameter_Name can be almost any string, but we have prefix conventions to keep things clear:

* ``Ma/`` for Materials
* ``El/`` for Elements
* ``Is/`` for Isotopes
* ``Ge/`` for Geometry Components
* ``So/`` for Particle Sources
* ``Ph/`` for Physics
* ``Ch/`` for Chemistry  (introduced for TOPAS-nBio)
* ``Mo/`` for Chemical Molecules (introduced for TOPAS-nBio IRT)
* ``Vr/`` for Variance Reduction
* ``Sc/`` for Scoring
* ``Gr/`` for Graphics
* ``Tf/`` for Time Features
* ``Ts/`` for TOPAS overall control

The Parameter_Type tells TOPAS what type of data will be in this parameter:

* ``d`` for Dimensioned Double
* ``u`` for Unitless Double
* ``i`` for Integer
* ``b`` for Boolean
* ``s`` for String
* ``dv`` for Dimensioned Double Vector
* similarly for ``uv``, ``iv``, ``bv`` and ``sv``
 

The Parameter_Name and Parameter_Value define what the line does. There are certain key parameters that will initiate the construction of, for example, a scorer or a geometry, which then causes TOPAS to look for necessary connected parameters, e.g. the size of the object. 

Within TOPAS-nBio, many new components and options have been developed, the implementation and the required parameters are described on this site. For all other parameters covered by TOPAS, please refer to the OpenTOPAS documentation: `TOPAS Parameter System`_.


.. _TOPAS Parameter System: https://opentopas.readthedocs.io/en/latest/parameters/intro/index.html
