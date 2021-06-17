# CPSign Documentation

This project is used for documenting CPSign using [Sphinx](http://www.sphinx-doc.org/en/stable/). Pages are written in [reStructuredText](http://docutils.sourceforge.net/rst.html) format.

## Build documentation locally

You need to have Python, [Sphinx](http://www.sphinx-doc.org/en/stable/) and the [ReadTheDocs scheme](https://github.com/snide/sphinx_rtd_theme) installed on your machine, which can be installed using `pip`: 
```
pip install --user sphinx
pip install --user sphinx-rtd-theme
```
Run `make html` from the base of the directory. This will build the html-files and put them in the `build`-subfolder. Open the `index.html` file in `build`-directory and
check your changes.

## Update 'live' documentation

The docs website is updated using two GitHub actions located in `.github/workflows/`:
- `build_and_push_docs.yaml`: triggers on git "Tag" starting with `v-` which creates a new sub-directory with the docs on Kuben for the given tag-id/version that should correspond to the version of CPSign.
- `build_latest.yaml`: triggers on push to branch `master` and updates the https://arosbio.com/cpsign/docs/latest/ pages.
