# UNLEASH: SOTA Semantic-based Log Parser with Pre-trained Language Models

__UNLEASH__ is and end-to-end semantic-based log parsing framework. This repository includes artifacts for reuse and reproduction of experimental results presented in our ICSE'25 paper titled _"Unleashing the True Potential of Semantic-based Log Parsing with Pre-trained Language Models"_.

__Table of Contents__
- [Repository Structure](#repository-structure)
- [Installation Instruction](#installation-instruction)
    - [Install Python 3.9](#install-python-39)
    - [Clone UNLEASH from GitHub](#clone-unleash-from-github)
    - [Create and activate a virtual environment](#create-and-activate-a-virtual-environment)
    - [Install UNLEASH from PyPI or Build from source](#install-unleash-from-pypi-or-build-from-source)
    - [Test the installation](#test-the-installation)
- [To run the code](#to-run-the-code)
- [Reproducibility](#reproducibility)
    - [Parsing Performance](#parsing-performance)
    - [Scalability and Generalization](#scalability-and-generalization)
    - [Other Settings](#other-settings)
- [Download Paper](#download-paper)
- [Citation](#citation)
- [Contact](#contact)

## Repository Structure

There are three main components in the repository:
1. `datasets`: Contains the log datasets used in the experiments.
2. `examples`: Contains the scripts to run the experiments.
3. `unleash`: Contains the implementation of UNLEASH.

<details>
<Summary>The main structure of the repository would look like this</Summary>

```
📦 UNLEASH
├─ LICENSE
├─ README.md
├─ datasets
│  └─ loghub-2.0
│     ├─ Apache
│     │  ├─ Apache_full.log
│     │  ├─ Apache_full.log_structured.csv
│     │  ├─ Apache_full.log_structured_corrected.csv
│     │  ├─ Apache_full.log_templates.csv
│     │  └─ Apache_full.log_templates_corrected.csv
│     ├─ ...
├─ docs
│  ├─ CL.png
│  ├─ Ob2_res.png
│  ├─ Ob3_res.png
│  ├─ RESULTS.md
│  └─ S_test_1.png
├─ environment.yml
├─ examples
│  ├─ 01_sampling.py
│  ├─ 02_run_unleash.py
│  ├─ 03_evaluation.py
│  ├─ benchmark.py
│  └─ config.py
├─ requirements.txt
├─ setup.py
├─ tests
│  └─ test.py
└─ unleash
   ├─ __init__.py
   ├─ arguments.py
   ├─ data
   │  ├─ __init__.py
   │  ├─ data_loader.py
   │  └─ utils.py
   ├─ evaluation
   │  ├─ settings.py
   │  └─ utils
   │     ├─ GA_calculator.py
   │     ├─ PA_calculator.py
   │     ├─ common.py
   │     ├─ evaluator_main.py
   │     ├─ oracle_template_correction.py
   │     ├─ post_process.py
   │     ├─ postprocess.py
   │     └─ template_level_analysis.py
   ├─ models
   │  ├─ __init__.py
   │  ├─ base.py
   │  ├─ deberta.py
   │  └─ roberta.py
   ├─ parsing_base.py
   ├─ parsing_cache.py
   ├─ postprocess.py
   ├─ sampling
   │  ├─ __init__.py
   │  ├─ entropy_sampling.py
   │  ├─ lilac_sampling.py
   │  ├─ logppt_sampling.py
   │  └─ utils.py
   └─ tuning
      ├─ __init__.py
      ├─ early_stopping.py
      ├─ trainer.py
      └─ utils.py
```
</details>


## Installation Instruction
The code is implemented in Python 3.9.

### Install Python 3.9
We recommend using Python 3.9+ to run the code.
```bash
sudo apt update
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt install python3.9 python3.9-venv python3.9-dev
```

### Clone UNLEASH from GitHub

```bash
git clone https://github.com/LogIntelligence/UNLEASH.git && cd UNLEASH
```

### Create and activate a virtual environment
We recommend creating a virtual environment to run the code.
```bash
python3.9 -m venv env
source env/bin/activate
```

### Install UNLEASH from PyPI or Build from source
You can install UNLEASH from PyPI or build from source.
```bash
# Install from PyPI
pip install unleash

# Build from source
pip install -e .
```

### Test the installation
```bash
pytest tests/test.py
```

<details>
<Summary>Expected output</Summary>

```bash
============================== test session starts ===============================
platform linux -- Python 3.9.21, pytest-8.3.4, pluggy-1.5.0
rootdir: /home/ubuntu/Documents/UNLEASH
collected 3 items                                                                

tests/test.py ...                                                          [100%]

=============================== 3 passed in 3.93s ================================
```
</details>

## To run the code
To perform log parsing on a specific dataset, you need to set the `dataset` parameter and set the working directory to the `examples` folder.
```bash
export dataset=Apache
cd examples
```

### 1. Run sampling for a specific dataset
```bash
python 01_sampling.py --dataset $dataset --sampling_method unleash
```

### 2. Run UNLEASH on a specific dataset
```bash
python 02_run_unleash.py --log_file ../datasets/loghub-2.0/$dataset/${dataset}_full.log_structured.csv --model_name_or_path roberta-base --train_file ../datasets/loghub-2.0/$dataset/samples/unleash_32.json --validation_file ../datasets/loghub-2.0/$dataset/validation.json --dataset_name $dataset --parsing_num_processes 1 --output_dir ../results --max_train_steps 1000
```
Set `parsing_num_processes` to the number of CPU cores you want to use for parsing. The results will be saved in the `../results` folder.

### 3. Evaluate Unleash on a specific dataset
```bash
python 03_evaluation.py --output_dir ../results --dataset $dataset
```
<details>
<Summary>Expected output</Summary>

```bash
=== Evaluation on Apache ===
../results/logs/Apache_full.log_structured.csv
Start to align with null values
100%|████████████████████████████████████████████████████| 51978/51978 [00:00<00:00, 220944.35it/s]
100%|████████████████████████████████████████████████████| 51978/51978 [00:00<00:00, 220116.95it/s]
Start compute grouping accuracy
100%|████████████████████████████████████████████████████████████| 30/30 [00:00<00:00, 1057.17it/s]
Grouping_Accuracy (GA): 1.0000, FGA: 1.0000,
Grouping Accuracy calculation done. [Time taken: 0.039]
Parsing_Accuracy (PA): 0.9953
Parsing Accuracy calculation done. [Time taken: 0.002]
100%|███████████████████████████████████████████████████████████| 30/30 [00:00<00:00, 14847.09it/s]
PTA: 0.8000, RTA: 0.8000 FTA: 0.8000
Identify : 30, Groundtruth : 30
Template-level accuracy calculation done. [Time taken: 0.010]
```
</details>

## Reproducibility

### Parsing Performance

To reproduce the parsing performance, you can run the following command:
```bash
cd examples
bash benchmark.sh
```

The parsing accuracy (`parsing_accuracy.csv`) and parsing time (`time_cost.json`) will be saved in the corresponding folders in the `../results` directory (e.g., `../results/iteration_01/logs`). 

### Scalability and Generalization

- Scalability: The scalability of UNLEASH is reflected in the parsing time and accuracy with different numbers of parsing processes. To run UNLEASH with different numbers of parsing processes, you can set the `parsing_num_processes` parameter in the `02_run_unleash.py` script and run [Step 2](#2-run-unleash-on-a-specific-dataset) again:
```bash
export num_processes=4

python 02_run_unleash.py --log_file ../datasets/loghub-2.0/$dataset/${dataset}_full.log_structured.csv --model_name_or_path roberta-base --train_file ../datasets/loghub-2.0/$dataset/samples/unleash_32.json --validation_file ../datasets/loghub-2.0/$dataset/validation.json --dataset_name $dataset --parsing_num_processes $num_processes --output_dir ../results --max_train_steps 1000
```

- Generalization: The generalization of UNLEASH is reflected in the parsing accuracy on different pre-trained language models and numbers of training examples.

    - To run UNLEASH with different pre-trained language models, you can set the `model_name_or_path` parameter in the `02_run_unleash.py` script and run [Step 2](#2-run-unleash-on-a-specific-dataset) again:

    ```bash
    export model_name="roberta-base" # currently, we support roberta-base, microsoft/deberta-base, microsoft/codebert-base, and huggingface/CodeBERTa-small-v1
    python 02_run_unleash.py --log_file ../datasets/loghub-2.0/$dataset/${dataset}_full.log_structured.csv --model_name_or_path $model_name --train_file ../datasets/loghub-2.0/$dataset/samples/unleash_32.json --validation_file ../datasets/loghub-2.0/$dataset/validation.json --dataset_name $dataset --parsing_num_processes 1 --output_dir ../results --max_train_steps 1000
    ```

    - To run UNLEASH with different numbers of training examples, you can set the `train_file` parameter in the `02_run_unleash.py` script and run [Step 2](#2-run-unleash-on-a-specific-dataset) again:

    ```bash
    export shot=64 # can be [32, 64, 128, 256]
    python 02_run_unleash.py --log_file ../datasets/loghub-2.0/$dataset/${dataset}_full.log_structured.csv --model_name_or_path roberta-base --train_file ../datasets/loghub-2.0/$dataset/samples/unleash_$shot.json --validation_file ../datasets/loghub-2.0/$dataset/validation.json --dataset_name $dataset --parsing_num_processes 1 --output_dir ../results --max_train_steps 1000
    ```

### Other Settings

UNLEASH provides various settings to customize the parsing process. You can set the following **main parameters**:
- For sampling (Step 1 - `01_sampling.py`):
    - `sampling_method`: The sampling method to use for selecting training examples. Currently, we support `unleash`, `lilac`, and `logppt`. To sample using all methods, set `sampling_method` to `all`.
- For parsing (Step 2 - `02_run_unleash.py`):
    - `model_name_or_path`: The pre-trained language model to use for parsing. Currently, we support `roberta-base`, `microsoft/deberta-base`, `microsoft/codebert-base`, and `huggingface/CodeBERTa-small-v1`.
    - `train_file`: The path to the training examples.
    - `max_train_steps`: The maximum number of training steps.
    - `save_model`: Whether to save the trained model.
    - `parsing_num_processes`: The number of parsing processes to use for parsing.



## Download Paper

The paper is available at [ICSE_25___Unleash.pdf](ICSE_25___Unleash.pdf).

## Citation

```
@inproceedings{le2025unleash,
  title={Unleashing the True Potential of Semantic-based Log Parsing with Pre-trained Language Models},
  author={Le, Van-Hoang and Xiao, Yi and Zhang, Hongyu},
  booktitle={Proceedings of the 47th International Conference on Software Engineering},
  year={2025}
}
```

## Contact

For any questions, please contact [Van-Hoang Le](mailto:levanhoang.psa@gmail.com).