# SDD
SDD - Standard data format to record DNA Damage

The documentation is written in reStructuredText format (reST). It is hosted by ReadTheDocs. A good resource on reST is the Sphinx documentation, but please note that not all features described there are supported by ReadTheDocs. It also describes some Python-only features, since that is its domain.

If working on a Mac, I recommend installing Python with Homebrew to avoid messing up your system Python.

To build and view the docs locally (recommended for substantial editing), you will need to

pip install sphinx sphinx-autobuild sphinx_rtd_theme
pip install -e git://github.com/jschuemann/SDD.git
then, after your edits

make clean
make html
open .build/html/index.html

If you need a .rst renderer you can use http://rst.ninjs.org/ for quick visualisation.
