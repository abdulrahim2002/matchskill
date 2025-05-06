### Match skills of contributors in open source projects to issues

Overview of the research project:

1. Domain labels can be generated for open issues reliably, [santos et.
   al.](https://ieeexplore.ieee.org/abstract/document/9463078/), however
   it is still on the onus of the developer to find a task they can work
   on
2. This project aims to automatically match contributors to issues they
   are capable of solving, based on their contribution history

### Objectives:

- Generate domain labels for closed issues using existing models
- Generate developer profiles using contribution history of the
  developer, using data of issues they solved in the past and the domain
  labels associated with them
- Find open issues a particular developer is capable of solving based on
  their profile
- Or alternatively, find developers who can solve a particular open
  issue

## Current capabilities

- We can generate ground truth using spacy library, and use the random
  forest model to predict issues both open and closed. [Code is in this
  notebook](RF_predictions_with_GT_by_spacy.ipynb)
- We can generate frequency of domain labels, each developer engaged in.
  That is, we can generate mapping of:

  `developer -> domain -> frequency of engagement`
  
  for each developer and domain

  This mapping can help us to determine weather a particular developer
  is fit for solving issues of a particular domain. Please see [example
  output](output/JabRef_JabRef_developer_stastics.csv)

### Installation and running the code

To code here is currently based on modified version of
[ART-CoreEngine](https://github.com/RESHAPELab/ART-CoreEngine).

The following changes are made to the Core Engine:

1. Use of OPENAI API keys is no longer required to run the random forest
   model
2. Abstract syntax tree can now be generated on the fly, rather than
   first downloading the file from github and then running the AST
   generation program on the downloaded file. It can now be directly
   passed the AST generate function as a string and it would return the
   AST object
3. Some of the functionalities might not work as expected 

### Running the notebooks

#### Linux

Generate virtual environment using:

```
python -m venv {name_of_your_virtual_environment}
```

Activate the virtual environment:

```
source {path_to_you_virutal_environment}/bin/activate
```

Install ipykernel and jupyter (for running noteboooks) lab:

```
# run after activating your virtual environment
pip install ipykernel jupyter-lab
```

Register your virtual environment as a kernel in jupyter:

```
# run after activating your virtual environment. Name it as you like
python -m ipykernel install --user --name={any_name} --displayname "{any_name}"
```

In the project root directory, run:

```
# run after activating your virtual environment
jupyter-lab
```

## To generate developer stastics

- Either directly run the
  [get_developer_stastics.py](src/get_developer_stats.py) file or 
- Use as an API from CoreEngine

``` python
import src as CoreEngine

data_frame = CoreEngine.get_developer_stats( repo_owner = "jabref",
                                             repo_name = "jabref",
                                             access_token = "github_token",
                                             openai_key = "not required",
                                             limit = 100 )

# use the dataframe afterwords
```

## To setup the the core engine:

1. Run `poetry install` -- this sets up the virtual environment
2. Run `poetry run python3 -m spacy download en_core_web_md` to download
   the language file
3. Create a GitHub Personal Access Token to use for downloading issues
   from GitHub and save it in a file
4. Set up a configuration file for training like below (see [example
   pre-filled configuration](/input/config_example.json) for default)
5. Set an environment variable in a `.env` file with the
   `OPENAI_API_KEY` set to an OpenAI key # NOT REQUIRED FOR RF MODEL 
6. Place a GitHub key in a file located at `auth_path` as specified in
   the `config.json`. (Default: `input/mp_auth.txt`)
7. Run `poetry run python3 main.py path/to/config.json` where the json
   is the one set up from step three. This will download, analyze, and
   train the model. It stores the results in a cache, preventing
   repeated calls.

SAVE `ai_result_backup.db` in the `output` directory as this keeps track
of AI artifacts. Deleting this file can result in having to redo OpenAI
calls, costing money!

> :information_source: **Info**<br>

If you want to restart the analysis from a clean state, delete **ONLY**
the `main.db` file in the `output` directory. You should rarely have to
delete `main.db`, except when switching repositories. `main.db` caches
all extracted data to prevent re-download.


> ### Note on opening CSV files in the project: None of the CSV files
> ### would open in regular csv readers. Because the CSV's are delimited
> ### by '\a'. It is recommended to load them in dataframe, to see their
> ### contents or use them
