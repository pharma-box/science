# Patient Survey Analysis

Jupyter Notebooks and code used for analyzing survey response data and research

## Notebooks

### [scrubb-data](./scrubb-data.md)

Scrubs responses from [this user survey](https://docs.google.com/forms/d/e/1FAIpQLSd_PKeGdo52yy1-53cq4eu5tdNZxq6UOGox7kNnRm41cuR5MQ/viewform) so that data can be shared and analyzed without exposing information about respondents.

### [patient-analysis](./patient-analysis.md)

Data analysis for [this user survey](https://docs.google.com/forms/d/e/1FAIpQLSd_PKeGdo52yy1-53cq4eu5tdNZxq6UOGox7kNnRm41cuR5MQ/viewform) to derive conclusions on responses

## Development

The easiest way to get started is to run the `Makefile`. Available commands can be seen by typing `make` in the current directory `/science/patient-survey-analysis`. You need to have [Python](https://www.python.org/downloads/) and [CMake](https://cmake.org/) installed. `CMake` should be installed by default if you are using a `*.nix` operating system.

**Available Commands**:

1. `install` ....... installs all the required dependencies using pip

2. `start` ....... starts all notebooks

3. `list` ....... list active notebooks

4. `down` ....... shutdown notebooks on 8888

5. `markdown` ....... generates markdown files for targeted notebooks. To add a notebook as a markdown target, add the following line to the `Makefile`:

```Makefile
markdown:
	jupyter nbconvert ./<insert_notebook_name>.ipynb --to markdown --output <insert_notebook_name>.md
```
