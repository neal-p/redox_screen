# Redox_Screen
Workflow for screening ground state and excited state redox properties

## Requirements:
  1. This workflow uses the workflowV2 codebase. Be sure that you have an active conda envirionment set up to use it before attempting to screen any redox potentials
  2. A SMILES string with at least 1 [Y] attachment point for substitution (if no substitution is desired, then add one anyway, and you can just substitute with 'H'

## How to get this code:
Download the scripts to your home directory:
```
cd
git clone
```

## How to use this code:

**SETUP**
```
cd redox_screen
ls -la
```
This `redox_screen.sbatch` file is the main file for the user to interact with. This script submits the workflow with the passed input arguments

You should copy the `redox_screen.sbatch` file to where you want to run the screen

```
cd /scratch/$USER
mkdir new_redox_screening_set
cd new_redox_screening_set
cp ~/redox_screen/redox_screen.sbatch ./
```

Modify the SBATCH parameters and calculation details to suit your input molecules:

`head -n redox_screen.sbatch`

>#!/usr/bin/env python\
>#SBATCH --job-name=redox_screen\
>#SBATCH --output=out-%A\
>#SBATCH --error=error-%A\
>#SBATCH --partition=lopez\
>#SBATCH --nodes=1\
>#SBATCH --cpus-per-task=9\
>#SBATCH --mem=20G\
>#SBATCH --time=14-00:00:00\
>\
>#calculation details:\
>solvent = 'acetonitrile'\
>dftmethod = 'B3LYP/6-311+G(d,p) empiricaldispersion=gd3bj'\
>preopt_method = 'B3LYP/6-31G(d,p) empiricaldispersion=gd3bj'\
>tddftmethod = 'wb97xd/6-31+G(d,p)'\
>nproc = 24\
>mem = 120\
>time = '5-00:00:00'\
>partition = 'lopez'\
>\

For example, you can name your job something more descriptive than 'redox_screen', change the time limit for the workflow etc in the `#SBATCH` blocks

Or, if you need to change calculation details, such as the solvent, method, calculation time etc, they can be changed in the `calculation details` section

**RUN**\
To run the `redox_screen.sbatch` you must provide, at minimum, a smiles string with the `-s/--smiles`  argument. 

`sbatch redox_screen.sbatch -i O=C1C2=CC([Y])=C([Y])C=C2OC3=C1C=C([Y])C([Y])=C3`

However, there are a bunch of optional arguments for specifying the substitutents to add, molecule name, etc:

>usage: REDOX_SCREEN: Organic Photosensitizer workflow \
>       -s SMILES [-h] [--mol_name MOL_NAME]\
>       [--substituents SUBSTITUENTS [SUBSTITUENTS ...]]\
>       [--names NAMES [NAMES ...]] [--dummy DUMMY] [--nconfs NCONFS]\
>       [--no_reduction] [--no_oxidation]\
>       \
>required arguments:\
>  -s SMILES, --smiles SMILES\
> \
>optional arguments:\
>  -h, --help            show this help message and exit \
>  --mol_name MOL_NAME \
>  --substituents SUBSTITUENTS [SUBSTITUENTS ...]\
>  --names NAMES [NAMES ...]\
>  --dummy DUMMY\
>  --nconfs NCONFS\
>  --no_oxidation\


Since these commandlines can get length, you can store your arguments in a file and tell `redox_screen.sbatch` to parse arguments from that file with the '@' character:

```cat my_args.txt```
>--smiles=O=C1C2=CC([Y])=C([Y])C=C2OC3=C1C=C([Y])C([Y])=C3 \
>--mol_name=xanthone\
>--substituents\
>[*:1][H]\
>[*:1]OC\
>[*:1]C\
>[*:1]C(F)(F)F\
>[*:1]F\
>[*:1]Cl\
>[*:1]Br\
>[:1]C(C)(C)C\
>[:1]c1ccccc1\
>--names\
>H\
>OMe\
>Me\
>CF3\
>F\
>Cl\
>Br\
>t-bu\
>Ph


`sbatch redox_screen.sbatch @my_args.txt`

## Output
The calculations are all in run out of the directory, so all of the raw log 

Additionally two csvs are written:
  1. <mol_name>-output_energies.csv - which contains ALL of the intermediate result energies
  2. <mol_name>-output_energies-clean.csv - which is just the essential results (aka the actual potentials and just the first few excited states

 
