# CPSign Documentation

This project is used for documenting CPSign using [Sphinx](http://www.sphinx-doc.org/en/stable/). Pages are written in Markdown but were initially written i [reStructuredText](http://docutils.sourceforge.net/rst.html) and converted, so there might be remnants that may need to updated.


## Build documentation locally

### Installation of required packages

You need to have Python, [Sphinx](http://www.sphinx-doc.org/en/stable/) and the [ReadTheDocs scheme](https://github.com/snide/sphinx_rtd_theme) installed on your machine, which can either be installed using `pip`: 
```
pip install --user sphinx
pip install --user sphinx-rtd-theme
```
Or by `conda` using the supplied conda environment file:
```
conda env create -f conda.env.yml
```

### Run build

Run `make html` from the root directory of the repository. This will build the html-files and put them in the `build/html` subfolder. Open the `index.html` file and check your changes.

## Update 'live' documentation

The documentation is now hosted a [ReadTheDocs](https://cpsign.readthedocs.io/en/latest/) and the build is handled using a webhook that monitors changes pushed to github. Multiple versions of the documentation should be handled by using tags and sorted by the readthedocs website.