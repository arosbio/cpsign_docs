# CPSign Documentation

This project is used for documenting CPSign using [Sphinx](http://www.sphinx-doc.org/en/stable/). Pages are written in Markdown but were initially written i [reStructuredText](http://docutils.sourceforge.net/rst.html) and converted, so there might be remnants that may need to updated.


## Build documentation locally

### Installation of required packages

You need to have Python, and install the dependencies in the [requirements](docs/requirements.txt) file. 

### Run build

Building the documentation should be performed from the `docs` directory, where it can be achieved by running `make html`.  This will build the html-files and put them in the `docs/build/html` subfolder. Open the `index.html` file and check your changes.

## Update 'live' documentation

The documentation is now hosted a [ReadTheDocs](https://cpsign.readthedocs.io/en/latest/) and the build is handled using a webhook that monitors changes pushed to github. Multiple versions of the documentation should be handled by using tags and sorted by the readthedocs website.