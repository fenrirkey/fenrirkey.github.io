# Website for Skyrim Community Shaders Documentation
---

# Requirements

[Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)
[mike](https://github.com/jimporter/mike) for versioning

# Installation

A virtual environment is recommended, either [natively with Python](https://docs.python.org/3/library/venv.html) or an environment manager like [Conda](https://anaconda.org/anaconda/conda) or [Mamba](https://mamba.readthedocs.io/en/latest/).
```
pip install mkdocs-material
pip install mike
```

# Usage

## Material for MkDocs

```mkdocs.yml``` provides website settings
Markdown files in ```docs/``` provides webpage content.
See the Material for MkDocs documentation for more information.

### Adding alternate languages

[Alternate languages](https://squidfunk.github.io/mkdocs-material/setup/changing-the-language/#site-language-selector) can be added to ```mkdocs.yml```.

## mike

If you want to make a change and publish a new version (i.e. 0.2):

```mike deploy --push --update-aliases 0.2 latest```