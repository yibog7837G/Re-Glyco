# Re-Glyco : A GlycoProtein Builder

[Re-Glyco](https://glycoshape.org/reglyco) is a tool we designed to restore the missing glycosylation on glycoproteins deposited in the RCSB PDB or in the EBI-EMBL AlphaFold protein structure database. To get started, upload your protein structure file or choose a pre-existing AlphaFold or PDB structure, and let Re-Glyco do the rest!
Currently supported function includes :
- N-GlcNAcylation
- O-GalNAcylation
- O-GlcNAcylation
- O-Fucosylation
- O-Mannosylation
- O-Glucosylation
- O-Xylosylation
- C-Mannosylation

This tool is currently hosted under [GlycoShape project](https://glycoshape.org/)

# Installation

```
sudo apt install build-essential
conda create -n reglyco python=3.12
conda activate reglyco
conda install conda-forge::gromacs 
pip install -r requirements.txt

curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustup update

cd glycors
maturin develop --release

```
# GlycoShape Database Preparation

Re-Glyco expects a GlycoShape database directory at the `data_dir` variable in `config.py`. The `dummy_database` directory in this repo is only for quick local smoke tests.

## Step 1 (detailed): Confirm prerequisites and local paths

1. **Data sources and access**
   - Ensure you can access the GAP (GlycanAnalysisPipeline) repository: https://github.com/Ojas-Singh/GlycanAnalysisPipeline
   - Ensure you can access the GlycoShape repository: https://github.com/Ojas-Singh/GlycoShape
   - Verify you can open their README files and the database-related scripts before proceeding.
2. **Plan the local database root directory**
   - Choose an absolute path for the final GlycoShape database root directory.
   - Confirm there is enough disk space for all glycan data, cluster files, and outputs.
   - Make sure the directory is readable/writable and its path will remain stable (you will set `data_dir` in `config.py` to this path).
3. **Prepare your custom IUPAC glycan list**
   - Create a plain-text list with one IUPAC glycan per line.
   - Keep naming consistent with GAP/GlycoShape conventions and avoid extra whitespace.
4. **Gather required credentials (if applicable)**
   - If you plan to let GAP fetch data from GlyTouCan or other services, prepare the required account/API keys.
   - If you only use local data, confirm this and skip credential setup.
5. **Readiness check**
   - GAP + GlycoShape repositories are accessible.
   - The database root path is final and writable.
   - The custom IUPAC list is ready.
   - Credentials are available if you need automated downloads.

Once all items above are ready, proceed to the next steps.

## Next steps (overview)
- Configure and run GAP to generate intermediate/output data.
- Run the GlycoShape `DB_scripts` to build the database structure.
- Ensure the database root contains `GLYCOSHAPE.json`, `GLYCAN_TYPE.json`, and glycan folders (use `dummy_database` as a reference).
- Update `config.py` to point `data_dir` to the database root (absolute path; trailing slash is optional and is normalized).
- Validate via the API (first with an existing glycan, then with a custom glycan).
- If validation fails, fix missing files or directory names to match the expected structure.

# Running the API 

Modify the config.py to locate the GlycoShape Database directory.

```
gunicorn -w 4 api:app --timeout 900
```

if you want to run on different port
```
gunicorn -w 4 api:app --timeout 1500 -b 127.0.0.1:8001 
```

# Frontend

The current version of Re-Glyco only has backend API service which can be used by [GlycoShape Website](https://glycoshape.org) code of the frontend website is [here](https://github.com/Ojas-Singh/GlycoShape).

You can also use python script to access the API upload your pdb file and glycosylate it, a dummy code is provided as demo.py in this repo.


# Demo
Once the API is running locally at your desired port.
change the API_BASE_URL = "https://glycoshape.org"  in demo.py to "http://127.0.0.1:8000"

and run using 
```
python demo.py
```

This will upload the [pdb](AF-P29016-F1-model_v4.pdb) to the API and will produced glycosylated structures can be found at output folder or temp_files.

It can take upto 5 minute for a job.

# Citation

All of the data provided is freely available for academic use under Creative Commons Attribution 4.0 (CC BY-NC-ND 4.0 Deed) licence terms. Please contact us at elisa.fadda@mu.ie for Commercial licence. If you use this resource, please cite the following papers:

Callum M Ives and Ojas Singh et al. Restoring Protein Glycosylation with GlycoShape [bioRxiv (2023)](https://www.biorxiv.org/content/10.1101/2023.12.11.571101v1.full).

# Future roadmap
- CLI interface
- Density fitting
- Fitness function with Non-bonded interaction.

