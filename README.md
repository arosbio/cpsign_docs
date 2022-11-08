# CPSign Documentation

This project is used for documenting CPSign using [Sphinx](http://www.sphinx-doc.org/en/stable/). Pages are written in [reStructuredText](http://docutils.sourceforge.net/rst.html) format.

## Header conventions
Note that reStructuredText does not define any unique order of how headers/titles should be shown, instead it's based on the order they appear in each document. For convenience we should use the conventions outlined in the [Python dev style guide](https://devguide.python.org/documenting/#sections):

- \# with overline, for parts
- \* with overline, for chapters
- =, for sections
- -, for subsections
- ^, for subsubsections
- ", for paragraphs

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

The docs website is updated using two GitHub actions located in `.github/workflows/`:
- `build_and_push_docs.yaml`: triggers on git "Tag" starting with `v-` which creates a new sub-directory with the docs on Kuben for the given tag-id/version that should correspond to the version of CPSign.
- `build_latest.yaml`: triggers on push to branch `master` and updates the https://arosbio.com/cpsign/docs/latest/ pages.
