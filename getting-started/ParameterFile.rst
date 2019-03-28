Parameter File System
======================

TOPAS-nBio is an extension to TOPAS, which follows a consistent set of design paradigms. TOPAS is controlled by a simple text file which is used to specify the all the simulation parameters.  

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
* ``Ch/`` for Chemistry
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
 


.. _TOPAS Parameter System: https://topas.readthedocs.io/en/latest/parameters/intro/index.html
