# RSE Project 2

The project aims to analyze EEG data from the Emotiv EPOC device, focusing on the identification of independent components and the extraction of relevant features. The workflow processes the data, generates visualizations, and provides insights into the data through a comprehensive report.

For more information, see the [requirements](docs/requirements.md), [component overview](docs/component.md) and [directed acyclic graph](docs/dag.png) of the workflow.

## Table of Contents

- [Table of Contents](#table-of-contents)
- [Introduction](#introduction)
  - [User-Stories](#user-stories)
- [Usage](#usage)
  - [Running the Workflow](#running-the-workflow)
  - [Configuration](#configuration)
    - [Section Generation](#section-generation)
  - [Cleaning Up](#cleaning-up)
  - [Running Specific Steps](#running-specific-steps)
  - [Developer Guide](#developer-guide)
- [Data Source and License](#data-source-and-license)
  - [Source of EPOC Data](#source-of-epoc-data)
  - [Data License](#data-license)
- [Contributing Guidelines](#contributing-guidelines)
- [Contact Information](#contact-information)
- [Citation](#citation)
- [License](#license)

## Introduction

This project was created to provide researchers in cognitive science, neuroscience, data science, clinicians, and any other discipline working with EEG data with an automated, customizable, state-of-the-art EEG processing workflow. EEG is a widespread tool for non-invasively investigating neural processing, but with few tools implementing standardized workflows despite the fact that most EEG analyses involve the same steps. By creating an automated workflow, researchers will be able to accelerate their research progress, while ensuring reproducibility and consistency in processing and analysis.

### User-Stories:

Some example user stories include:

As a researcher, I want to automate most steps in the workflow, to improve reproducibility and consistency in preprocessing and analyses.

As a researcher, I want to inspect my (preprocessed) data as a quality measure.

As a junior researcher, I want to execute a complex workflow with just one command.

As a researcher, I want to see how strong signal differences in three predefined brain regions are in order to understand localisation of processing in the brain.

As a researcher, I want to generate statistical measures of the data and analyze them for effects.

As a senior researcher, I want to automatically generate a PDF report, showing the results of the workflow.

As a neurologist, I want to be able to selectively remove artifacts from the data, so as to not hinder further clinical analyses.

...

For more user stories see the [requirements](docs/requirements.md) file.

## Usage

Ensure you have the following prerequisites installed on your system:

- **[Conda](https://docs.anaconda.com/miniconda/)**: For environment management.
- **[Snakemake](https://snakemake.readthedocs.io/en/stable/)**: For workflow management.
- **[PDFLaTeX](https://www.latex-project.org/get/)**: For generating the final report.
- **[GNU Make](https://www.gnu.org/software/make/)**: For task automation. (Optional, but recommended)

### Running the Workflow

To run the entire workflow, execute the following command from the root directory of the repository:

```sh
make
```

This command will initiate the workflow and handle all necessary steps automatically. The first execution of the workflow will take some time, as all environments have to be created by conda. Subsequent runs will be substantially faster. Upon the first run, a configuration file (`config/config.yaml`) will be created if it does not already exist.

Alternatively, you can copy the configuration file from the template and run the workflow manually:

```sh
cp config/config.yaml.example config/config.yaml
snakemake --use-conda --cores <num_cores>
```

### Configuration

The configuration file (`config/config.yaml`) contains settings that can be customized to suit your needs:

**Mock Flag (`mock`)**:

```yaml
mock: <true|false>
```

When set to `true`, the workflow uses a reduced dataset for testing purposes, allowing for quicker iterations during development.

**Artifacts (`artifacts`)**:

```yaml
artifacts: "<artifact_index_1>,<artifact_index_2>,..."
```

Specifies a comma-separated list of artifact indices to exclude from processing, enhancing the flexibility in managing dataset quality.

**Section Titles (`section_titles`)**:

```yaml
section_titles:
  <key>: <human-readable-title>
  # ...
```

Defines human-readable titles for each section of the generated report. The key used in this dictionary also helps in dynamically generating corresponding `.tex` files from data processing results. The order in the configuration file preserves the order in the final report.

#### Section Generation

- **`denoised_epo_epochs`, `denoised_epo_psd`, `denoised_epo_raw`, and `denoised_epo_response`**: These sections are generated by appending "\_epochs", "\_psd", "\_raw", or "\_response" to the basename of `.fif` datasets in the `temp` directory. Each section represents a different analysis of the data:

  - `_epochs`: related to the epochs of the dataset.
  - `_psd`: pertaining to the power spectral density.
  - `_raw`: examination of raw dataset files.
  - `_response`: related to evoked responses.

- **`ica_components`**: This section is generated as part of the ICA workflow and represents the components identified through ICA processing.

- **`rq_1` to `rq_5`**: Each key corresponds to a specific research question, with the workflow generating sections that provide answers or insights related to these questions based on the data analysis results. The numeric suffix indicates the specific question being addressed.

### Cleaning Up

To remove all generated output and clean the output directories, use the following command:

```sh
make clean
```

### Running Specific Steps

To run specific steps of the workflow, use Snakemake's targeted execution:

```sh
snakemake --use-conda <target_rule>
```

If you wish to run a script or command outside of the workflow, you can activate the specific Conda environment manually:

```sh
conda env create --name <environment_name> -f workflow/envs/<environment_file>.yaml
conda activate <environment_name>
# Run your script or command here
```

For example, to run the Independent Component Analysis (ICA) script:

```sh
conda env create -f workflow/envs/ica.yaml
conda activate rse24_project-two_ica
python workflow/scripts/ica.py temp/truncated_data.feather ica_epo.fif plot.png
```

### Developer Guide

For developers looking to contribute or maintain the project, the following commands are available:

**Lint the Workflow**:
Ensure you have `snakefmt`, `autopep8`, and `flake8` installed. Run the following command to format and check the code:

```sh
make lint
```

**Generate Workflow Diagram**:
Create a visual representation of the workflow with:

```sh
make plot
```

**Run Tests**:
To execute the test suite, use the following command (requires `conda`):

```sh
make test
```

## Data Source and License

### Source of EPOC Data

This repository utilizes EPOC data from the MindBigData project, which offers a vast collection of brain signals. These signals were captured using various commercial EEG devices, including the Emotiv EPOC. The data includes over 910,000 signals of 2 seconds each, recorded during the stimuli of seeing and thinking about digits.

For more details, visit the [MindBigData Open Database](https://mindbigdata.com/opendb/index.html).

### Data License

The EPOC data is licensed under the Open Database License (ODbL) v1.0. Individual contents of the database are under the Database Contents License (DbCL) v1.0. This allows for sharing, modification, and use of the data, provided that proper attribution is given and any derivative works are also shared alike.

For full license details, refer to:

- [ODbL v1.0](http://opendatacommons.org/licenses/odbl/1.0/)
- [DbCL v1.0](http://opendatacommons.org/licenses/dbcl/1.0/)

## Contributing Guidelines

If you wish to contribute to the project, please review the [contribution guidelines](CONTRIBUTING.md) and the [code of conduct](CONDUCT.md). By participating, you are expected to adhere to these guidelines.

We use the [issues tab](https://gitup.uni-potsdam.de/werner10/rse24_project-two/-/issues) for tracking features and bugs.

## Contact Information

For any inquiries, please contact us at:

- [Harshini Eggoni](mailto:eggoni@uni-potsdam.de)
- [Philipp Freiherr von Entreß-Fürsteneck](mailto:entressfue@uni-potsdam.de)
- [Max Nowaczyk](mailto:nowaczyk2@uni-potsdam.de)
- [Tauqeer Kasam Rumaney](mailto:rumaney@uni-potsdam.de)
- [Tim Werner](mailto:tim.werner@uni-potsdam.de)

## Citation

For information on how to cite this project, please refer to the [citation file](CITATION.cff).

## License

This project is licensed under the [MIT License](LICENSE).