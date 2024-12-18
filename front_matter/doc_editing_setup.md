# Documentation Editing Setup

The documentation uses the [MyST](https://mystmd.org/) markup language. 

Install the following packages within an existing conda environment:
```{code} bash
conda install conda-forge::mystmd
conda install conda-forge::nodejs
conda install conda-forge::texlive-core
conda install conda-forge::latexmk
```

Check that MyST has been installed successfully by running
```{code} bash
myst -v
```

Run `myst` to render the documentation and click the local URL displayed on the terminal.
When editing source files, the local build will update automatically.
