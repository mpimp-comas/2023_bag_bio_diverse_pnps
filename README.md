# 2023_bag_bio_diverse_pnps

This repository contains the Jupyter Notebooks that were used to perform the cheminformatics analyses and generate the respective plots for the paper:

**A divergent intermediate strategy yields biologically diverse pseudo-natural products**  
Sukdev Bag, Michael Grigalunas, Jie Liu, Sohan Dilip Patil, Jana Bonowski, Beate Schölermann, Lin Wang, Axel Pahl, Sonja Sievers, Lukas Brieger, Carsten Strohmann, Slava Ziegler, and Herbert Waldmann*

It also contains a set of Notebook helper utilities (`jupy_tools/utils`) and scripts for compound structure standardization and filtering (`stand_struct.py`) as well as for the calculation of PMI values (under `python_scripts`).

RDKit was used for the cheminformatics calculations, Matplotlib and Seaborn were used for the visualizations. See the `environment.yml` file for the respective library versions used.

External data sets used from ChEMBL, Drugbank and Enamine are not included in this repository, but the steps that were applied for obtaining them are described and the scripts for the standardization of the structures are included here (see `notebooks/` folder).


### Installation

The installation has only been tested on Linux (Ubuntu 22.04).

1. Clone this repo and change into the directory
1. Create a new conda environment, install the dependencies and activate the environment:
    ```
    conda env create -f environment.yml
    conda activate bag  # if the name from the environment file was used
    ```
1. Link the RDKit's Contrib folder to one of Python's import paths. This is necessary for the calculation of the Natural Product Likeness score.
    e.g.:
    ```
    # e.g.:
    ln -s <path-to-conda-env>/share/RDKit/Contrib <path-to-conda-env>/lib/python3.9/site-packages/

    # concrete example:
    ln -s $HOME/anaconda3/envs/bag/share/RDKit/Contrib $HOME/anaconda3/envs/bag/lib/python3.9/site-packages/
    ```

1. Link the folder `jupy_tools` to the same folder (or use some other folder that is printed by `python3 -c "import sys; print(sys.path)"`)
    e.g.: `ln -s <path-to-this-repo>/jupy_tools <path-to-conda-env>/lib/python3.9/site-packages/`
(I actually prefer this to using setuptools, because a simple git pull will get the newest version). 


## Python Scripts
* [stand_struct.py](python_scripts/stand_struct.py): A Python script for standardizing structure files. The input can be either SD files OR CSV or TSV files which contain the structures as `Smiles`. 
The output is always a TSV file, various options are available, please have a look at the help in the file. 
* [calc_pmi.py](python_scripts/calc_pmi.py): Script for calculating PMI values.
* [extract_nps_from_sqlite.py](python_scripts/extract_nps_from_sqlite.py): Script to extract the Natural Products from ChEMBL. Both the SQLite db and the standardized ChEMBL (using `stand_struct.py`) data are required to be available in the same folder where this script is run. Run with `extract_nps_from_sqlite.py <ChEMBL_version>`.


### Usage

```
$ stand_struct --help
usage: stand_struct [-h] [--nocanon] [--min_heavy_atoms MIN_HEAVY_ATOMS] [--max_heavy_atoms MAX_HEAVY_ATOMS] [-d] [-c COLUMNS] [-n N] [-v]
                    in_file {full,fullrac,medchem,medchemrac,fullmurcko,medchemmurcko}

Standardize structures. Input files can be CSV, TSV with the structures in a `Smiles` column
or an SD file. The files may be gzipped.
All entries with failed molecules will be removed.
By default, duplicate entries will be removed by InChIKey (can be turned off with the `--keep_dupl` option)
and structure canonicalization will be performed (can be turned off with the `--nocanon`option),
where a timeout is enforced on the canonicalization if it takes longer than 2 seconds per structure.
Timed-out structures WILL NOT BE REMOVED, they are kept in their state before canonicalization.
Omitting structure canonicalization drastically improves the performance.
The output will be a tab-separated text file with SMILES.

Example:
Standardize the ChEMBL SDF download (gzipped), keep only MedChem atoms
and molecules between 3-50 heavy atoms, do not perform canonicalization:
    `$ ./stand_struct.py chembl_29.sdf.gz medchemrac --nocanon`
            

positional arguments:
  in_file               The optionally gzipped input file (CSV, TSV or SDF). Can also be a comma-separated list of file names.
  {full,fullrac,medchem,medchemrac,fullmurcko,medchemmurcko}
                        The output type. 'full': Full dataset, only standardized; 'fullrac': Like 'full', but with stereochemistry removed; 'fullmurcko':
                        Like 'fullrac', structures are reduced to their Murcko scaffolds; 'medchem': Dataset with MedChem filters applied, bounds for the
                        number of heavy atoms can be optionally given; 'medchemrac': Like 'medchem', but with stereochemistry removed; 'medchemmurcko':
                        Like 'medchemrac', structures are reduced to their Murcko scaffolds; (all filters, canonicalization and duplicate checks are
                        applied after Murcko generation).

optional arguments:
  -h, --help            show this help message and exit
  --nocanon             Turning off structure canonicalization greatly improves performance.
  --min_heavy_atoms MIN_HEAVY_ATOMS
                        The minimum number of heavy atoms for a molecule to be kept (default: 3).
  --max_heavy_atoms MAX_HEAVY_ATOMS
                        The maximum number of heavy atoms for a molecule to be kept (default: 50).
  -d, --keep_duplicates
                        Keep duplicates.
  -c COLUMNS, --columns COLUMNS
                        Comma-separated list of columns to keep (default: all).
  -n N                  Show info every `N` records (default: 1000).
  -v                    Turn on verbose status output.
  ```


## Jupyter Notebooks

The Jupyter Notebooks for the calculation of the chemical descriptors used in the Principal Component Analysis as well as for the calculation of the Principal Moment of Inertia can be found in the `notebooks` folder.
