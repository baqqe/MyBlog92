---
title: Building Continuous Integration Pipelines with GitHub Actions for Machine Learning
author: ''
date: '2024-11-27'
slug: github-actions
categories: []
tags: []
---

Continuous Integration (CI) has become a cornerstone of modern software development, enabling teams to automate testing, validate code changes, and ensure a seamless development workflow. In this post, we’ll explore how GitHub Actions can be used to create an automated CI pipeline for a machine learning project, walking through its core concepts and practical implementation.

## What is Continuous Integration (CI)?

Continuous Integration is a development practice where developers frequently merge their code changes into a shared repository. Each change triggers an automated process that includes:

* Building the application.

* Running tests to ensure new changes don’t break existing functionality.

* Validating artifacts for deployment.

CI helps catch bugs early, ensures code quality, and makes the integration process smoother for teams.


## Introducing GitHub Actions

GitHub Actions is a powerful automation platform directly integrated into GitHub repositories. It allows developers to define workflows that execute in response to events like code pushes, pull requests, or scheduled tasks. Using GitHub Actions, you can build, test, and deploy your code seamlessly.

Key features of GitHub Actions include:

- Workflow Automation: Define tasks in YAML files.

- Reusable Actions: Leverage pre-built community actions or create your own.

- Integration: Works natively with GitHub repositories, making setup straightforward.

## Setting Up CI for a Machine Learning Project

Let’s take a practical example: automating the training and testing of a machine learning model using GitHub Actions.

### The Project

We’ll use a Python-based project for predicting “Cycle Time” of LEGO injection molding processes. Our workflow will:

1) Run unit tests to validate the machine learning pipeline.

2) Save the trained model as an artifact for further use.

### Project Structure


This project is structured to facilitate both development and automation using GitHub Actions. Each component serves a specific role in the CI pipeline. Here's the breakdown:

- `model.py`: ML training script that includes loading data, training, testing, and saving the model.
- `test_model.py`: Unit test script validating the training process using `pytest`.
- `requirements.txt`: Python dependencies needed for the project.
- `lego_injection_molding_data.csv`: Dataset used for model training.
- `.github/workflows/ci.yml`: GitHub Actions workflow defining the CI pipeline.


### Key Components

Machine Learning Code (model.py)

The code includes:

1) Loading Data: Reading and preprocessing the dataset.

2) Training: Using a RandomForestRegressor to train a model.

3) Testing: Calculating the Mean Squared Error (MSE).

4) Saving: Storing the trained model for future use.

Unit Tests (test_model.py)

The test script uses pytest to validate the training pipeline, ensuring:

1) A valid model is trained.

2) The MSE metric is computed correctly.

CI Workflow (ci.yml)

This YAML file defines the steps for our CI pipeline:

1) Trigger on Push and Pull Requests. The workflow is triggered whenever there’s a push or pull request to the main branch.

2) Install Dependencies. It sets up Python, installs the required dependencies, and prepares the environment.

3) Run Tests. Executes pytest to validate the ML pipeline.

4) Save and Upload Model Artifact. Trains the model and uploads the resulting .pkl file as a reusable artifact.

### The CI Workflow in Detail

Here is the full ci.yml file:

```
name: CI Pipeline

# Triggers
on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.9'

    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install pytest
        pip install -r requirements.txt

    - name: Run tests
      run: python -m pytest test_model.py

    - name: Save trained model
      run: |
        python model.py
        mkdir -p artifacts
        mv trained_model.pkl artifacts/
    
    - name: Upload model artifact
      uses: actions/upload-artifact@v3
      with:
        name: trained-model
        path: artifacts/trained_model.pkl
```

#### Key Steps Explained

1) Triggering Events. The workflow triggers on push and pull_request events for the main branch, ensuring all changes are validated before merging.

2) Setting Up the Environment. The setup-python action installs Python 3.9. Dependencies like pytest and project requirements are installed in subsequent steps.

3) Running Tests. pytest executes the unit tests defined in test_model.py. If any test fails, the workflow halts, preventing broken code from being merged.

4) Saving and Uploading Artifacts. The trained model is saved in an artifacts directory and uploaded using upload-artifact. This ensures the model is accessible for future workflows or deployments.


## Benefits of Using GitHub Actions for CI in Machine Learning

Using GitHub Actions for CI in machine learning offers several key benefits. It automates the testing and validation of machine learning pipelines, significantly reducing manual effort and streamlining the development process. By ensuring that all code changes are thoroughly tested before integration, it enhances reliability and helps maintain the quality of the project. Additionally, the ability to upload artifacts, such as trained models, ensures reproducibility by providing consistent outputs for downstream tasks. Finally, GitHub Actions offers scalability, allowing workflows to be easily extended to include tasks such as data validation, hyperparameter tuning, or deployment, making it a robust solution for machine learning projects.

## Conclusion

CI is essential for maintaining high-quality machine learning projects. With GitHub Actions, you can easily integrate testing, training, and artifact management into your workflow. The combination of automation and flexibility ensures that your projects are robust, reliable, and ready for deploymen
