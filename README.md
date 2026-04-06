<p style="border:1px; border-style:solid; border-color:black; padding: 1em;">
CS-UH 3260 Software Analytics<br/>
Replication Study Guidelines<br/>
Dr. Sarah Nadi, NYUAD
</p>

# Replication 3 -- CS-UH-3260 Software Analytics


## Overview

This repo contains the scripts and data used for the `Replication 3` assignment, focusing on the paper *Flakify: A Black-Box, Language Model-based Predictor for Flaky Tests*. The scope of our replication includes RQ1 and examining test cases classified as flaky or non-flaky (from dataset and from running Flakify).


### 1. Project Title and Overview

- **Paper Title**: Flakify: A Black-Box, Language Model-based Predictor for Flaky Tests
- **Authors**: Sakina Fatima, Taher A. Ghaleb, and Lionel Briand
- **Replication Team**: Matthias Kebede and Muhammad Arhum
- **Course**: CS-UH 3260 Software Analytics, NYUAD
- **Brief Description**: 
  - Fatima et al. propose a novel black-box predictor, Flakify, that exclusively relies on test code using the pre-trained language model CodeBERT as a base. They fine-tune this model and subsequently evaluate it on the FlakeFlagger and IDoFT datasets of flaky test cases, which we seek to replicate in this study.
  - Our external partial replication covers RQ1 concerning the accuracy of the Flakify tool. We additionally look at the flaky/non-flaky classification of as per the provided FlakeFlagger dataset and specified *serval-uni-lu/FlakyCat* repository to check our agreement and look for any patterns among the tests.

### 2. Repository Structure

Document your repository structure clearly. Organize your repository using the following standard structure:

```
README                    # Documentation for your repository
datasets/                 # Original dataset files taken from the Flakify GitHub repository
replication_scripts/
  - Flakify_predictor_cross_validation.py          # Modified cross-validation script (Google Colab)
  - Flakify_predictor_per_project.py               # Modified per-project validation script (Google Colab)
outputs/
  - Replication 3 Q1 - Sheet1.csv                  # Comments on flaky/non-flaky labelling of random tests
  - cross-validation-idoft.csv                     # Calculated metrics from cross-validation on IDoFT
  - cross-validation-idoft-raw.csv                 # Raw data from tenfold executions
  - cross-validation-flake.csv                     # Calculated metrics from cross-validation on FlakeFlagger
  - cross-validation-flake-raw.csv                 # Raw data from tenfold executions
logs/                     # Console output (files labelled per training run)
notes/
```

**For each folder and file, provide a brief description of what it contains.**

### 3. Setup Instructions

- **Prerequisites**: Required software, tools, and versions
  - Installations of Python 3.8.5, git, and pip (and Java if using SmellDetector)
  - Eclipse IDE (2021-12 or more recent) (if using SmellDetector .jar files, not necessary for just doing the retraining)
  - If you don't have a powerful GPU, you might consider using Google Colab or a cloud VM
- **Installation Steps**: Step-by-step instructions to set up the environment
  - Clone this repository by running `git clone git@github.com:MatthiasKebede/Replication_3.git`
  - Clone the Flakify repository with `git clone git@github.com:uOttawa-Nanda-Lab/Flakify.git`
    - (on Windows 10/11) You may need to run `git config --global core.longpaths true` and set `LongPathsEnabled` in the Registry Editor to bypass the `MAX_PATH` limit of 260 characters
    - Consider running `git remote remove origin` to disconnect from the remote repo
    - Also consider moving or renaming the `/results` subdirectory to make sure you don't mix the new outputs up
  - Create a virtual environment for use with Flakify:
    - If `torch==1.5.0` is not found, try `python -m pip install torch==1.5.0 -f https://download.pytorch.org/whl/torch_stable.html` (or 'torch==1.5.0+cpu' if that still doesn't work)
  ```bash
  python -m venv .venv                   # create venv (consider using `py -3.8` on Windows)
  source .venv/bin/activate              # activate venv (`.venv/scripts/activate` on Windows)
  python -m pip install -r requirements.txt        # install libraries
  ```
- **Alternative Setup for Google Colab**:
  - Clone the Flakify repo and zip it or otherwise upload the `.zip` file to Google Drive
  - Open a Colab notebook and select the proper runtime (e.g. w/ GPU)
  - Mount your drive and allow permissions
  ```python
  from google.colab import drive
  drive.mount('/content/drive')
  ```
  - Unzip the repo in the Colab environment
  ```bash
  !unzip /content/drive/MyDrive/ColabNotebooks/Flakify.zip -d /content/
  ```
  - Navigate into the `src` directory
  ```bash
  %cd /content/Flakify/src
  ```
  - Consider using the following code to automate fixing the version mismatches (be careful of batch size)
  ```python
  import glob

  for filepath in glob.glob('/content/Flakify/src/*.py'):
      with open(filepath, 'r') as file:
          content = file.read()

      # Fix AdamW import
      content = content.replace(
          'from transformers import AdamW, AutoTokenizer, AutoModel, AutoConfig',
          'from torch.optim import AdamW\nfrom transformers import AutoTokenizer, AutoModel, AutoConfig'
      )

      # Increase batch size for better GPU utilization
      content = content.replace('batch_size = 2', 'batch_size = 232')

      # Replace batch_encode_plus with direct tokenizer call
      content = content.replace('tokenizer.batch_encode_plus(', 'tokenizer(')

      # Update padding and truncation arguments to avoid ValueError later
      content = content.replace('pad_to_max_length=True', "padding='max_length'")

      # Fix compute_class_weight positional arguments for newer scikit-learn versions
      content = content.replace(
          "compute_class_weight('balanced', np.unique",
          "compute_class_weight('balanced', classes=np.unique"
      )

      with open(filepath, 'w') as file:
          file.write(content)

  print('Successfully patched tokenizer, compute_class_weight, AdamW, and batch_size in all source files.')
  ```
  - Run the scripts with the correct file paths (IDoFT example):
  ```bash
  !python /content/Flakify/src/Flakify_predictor_cross_validation.py \
    /content/Flakify/dataset/IDoFT/Flakify_IDoFT_dataset.csv \
    /content/Flakify/results/Flakify_cross_validation_model_weights_on_IDoFT_dataset.pt \
    /content/Flakify/results/Flakify_cross_validation_results_on_IDoFT_dataset.csv
  ```
- **Running Instructions**
  - Navigate to the `Flakify/src` subdirectory (after installing the required libraries w/ `pip`)
  - Run the following commands to run the Smell Detector and generate the `Flakify_FlakeFlagger_dataset.csv` and `Flakify_IDoFT_dataset.csv` datasets (if needed):
  ```bash
  bash FlakifySmellsDetector.sh FlakeFlagger
  bash FlakifySmellsDetector.sh IDoFT
  ```
  - Run the following commands to run the cross-validation and get `.csv` and `.pt` output files:
    - Change the `input_data = df['final_code']` line to `'full code'` to switch away from the pre-processed run
    - If you get an issue loading the `microsoft/codebert-base` model, you can try downloading the [`config.json`, `merges.txt`, `pytorch_model.bin`, `vocab.json`] files from https://huggingface.co/microsoft/codebert-base/tree/main and placing them in a `Flakify/src/codebert-base` subfolder, then replace `model_name = "microsoft/codebert-base"` with `model_name = "./codebert-base"` in the Python file
    - If you get an error `"Torch not compiled with CUDA enabled"`, replace add `...if torch.cuda.is_available() else "cpu")` to line 27 (or reinstall `torch` w/ CUDA if you have an Nvidia GPU)
  ```bash
  bash Flakify_predictor_cross_validation.sh FlakeFlagger
  bash Flakify_predictor_cross_validation.sh IDoFT
 
  # or in PowerShell:
  python Flakify_predictor_cross_validation.py ../dataset/FlakeFlagger/Flakify_FlakeFlagger_dataset.csv ../results/Flakify_cross_validation_model_weights_on_FlakeFlagger_dataset.pt ../results Flakify_cross_validation_results_on_FlakeFlagger_dataset.csv
  python Flakify_predictor_cross_validation.py ../dataset/IDoFT/Flakify_IDoFT_dataset.csv ../results/Flakify_cross_validation_model_weights_on_IDoFT_dataset.pt ../results/Flakify_cross_validation_results_on_IDoFT_dataset.csv
  ```
  - Run the following commands to run the per-project validation:
    - Change the `input_data = df['final_code']` line to `'full code'` to switch away from the pre-processed run
  ```bash
  bash Flakify_predictor_per_project.sh FlakeFlagger
  bash Flakify_predictor_per_project.sh IDoFT
  ```
  - Save any console output to log files as needed
  - Check the resulting metrics (Precision, Recall, F1-score)

### 4. GenAI Usage

**GenAI Usage**: Briefly document any use of generative AI tools (e.g., ChatGPT, GitHub Copilot, Cursor) during the replication process. Include:

  - **ChatGPT**
    - Used to help troubleshoot environment setup (setting up separate Python 3.8.5 installation on Windows, resolving torch version not found & hugging-face schema error)
  - **Gemini**
    - Used to resolve issues with versioning mismatches in Google Colab due to preconfigured environment (in order to use PyTorch w/ CUDA)









## Grading Criteria for README

Your README will be evaluated based on the following aspects (Total: 40 points):

### 1. Completeness (10 points)
- [ ] All required sections are present
- [ ] Each section contains sufficient detail
- [ ] Repository structure is fully documented
- [ ] All files and folders are explained
- [ ] GenAI usage is documented (if any AI tools were used)

### 2. Clarity and Organization (5 points)
- [ ] Information is well-organized and easy to follow
- [ ] Instructions are clear and unambiguous
- [ ] Professional writing and formatting
- [ ] Proper use of markdown formatting (headers, code blocks, lists)

### 3. Setup and Reproducibility (10 points)
- [ ] Setup instructions are complete and accurate, i.e., we were able to rerun the scripts following your instructions and obtain the results you reported


## Best Practices

1. **Be Specific**: Include exact versions, paths, and commands rather than vague descriptions
2. **Keep It Updated**: Ensure the README reflects the current state of your repository
3. **Test Your Instructions**: Have someone else (or yourself in a fresh environment) follow the setup instructions
4. **Document AI Usage**: If you used any GenAI tools, be transparent about how they were used (e.g., understanding scripts, exploring datasets, understanding data fields)


## Acknowledgement

This guideline was developed with the assistance of [Cursor](https://www.cursor.com/), an AI-powered code editor. This tool was used to:

- Draft and refine this documentation iteratively
