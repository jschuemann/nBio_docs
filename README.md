# TOPAS-nBio
The nanometer scale extension for radiobiology of the TOPAS Monte Carlo system

<img src="https://github.com/jschuemann/nBio_docs/blob/95b38432fffe874cb48c3389365917418f1d0b86/images/nBio_Logo.png" width = 200>

The documentation is written in reStructuredText format (reST). It is hosted by ReadTheDocs. A good resource on reST is the Sphinx documentation, but please note that not all features described there are supported by ReadTheDocs. It also describes some Python-only features, since that is its domain.

If working on a Mac, I recommend installing Python with Homebrew to avoid messing up your system Python.

	brew install python
	brew install git

To build and view the docs locally (recommended for substantial editing), you will need to have Sphinx version 1.4.2 or newer and do:

	pip3 install sphinx sphinx-autobuild sphinx_rtd_theme

To install TOPAS pygments:

	pip3 install git+https://github.com/tmasilela/topas-pygments.git

then, after your edits

	make clean
	make html
	open .build/html/index.html

If you need a .rst renderer you can use http://rst.ninjs.org/ for quick visualisation.
