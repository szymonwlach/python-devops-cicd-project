# 🐍 Python for DevOps: CI/CD for Python Projects

This repository contains the implementation of a complete **CI/CD pipeline** for Python projects, developed as part of the **DevOps course**.  
It showcases how to automate testing, linting, static analysis, building, and publishing Python packages to both **TestPyPI** and **PyPI**.

---

## 🚀 Overview

The project demonstrates a robust, production-ready **CI/CD workflow** using **GitHub Actions**, integrating modern tools for:
- Code quality enforcement
- Static and security analysis
- Automated testing
- Semantic versioning
- Automated publishing on release

---

## 🧩 Features Implemented

- ✅ Implemented project codebase  
- ✅ Added a **GitHub Actions workflow** (`python-ci.yaml`) for CI/CD  
- ✅ Integrated **linting** with [ruff](https://github.com/astral-sh/ruff)  
- ✅ Applied **code formatting checks** using [black](https://github.com/psf/black)  
- ✅ Added **type checking** with [mypy](https://github.com/python/mypy)  
- ✅ Included **security scanning** via [bandit](https://github.com/PyCQA/bandit)  
- ✅ Added **automated testing** with [pytest](https://pytest.org)  
- ✅ Configured **semantic-release** for automated versioning and publishing  
- ✅ Set up publishing to both **TestPyPI** and **PyPI** when a new release is published  

---

## ⚙️ Technologies Used

- **Python 3.9 – 3.12**  
- **GitHub Actions** for CI/CD  
- **ruff**, **black**, **mypy**, **bandit**  
- **pytest** for test automation  
- **python-semantic-release** for version management  
- **build** and **twine** for packaging  

---

## 🧱 GitHub Actions Workflow

Below is the workflow configuration used for this project, located at  
`.github/workflows/python-ci.yaml`:

```yaml
name: CI/CD for simple-http-checker

on:
  push:
    branches: ['main']
  workflow_dispatch:

jobs:
  lint-static-checks:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      id-token: write
    steps:
      - name: Check out repo
        uses: actions/checkout@v4
        with:
          fetch-depth: 0
          token: ${{ secrets.GH_PAT }}

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.12'

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install ".[dev]"
          pip install python-semantic-release

      - name: Create release
        env:
          GH_TOKEN: ${{ secrets.GH_PAT }}
        run: |
          semantic-release version
          semantic-release publish

      - name: Linting and format checks
        run: |
          ruff check .
          black --check .

      - name: Static type checks
        run: |
          mypy src/
          mypy --config-file pyproject.toml src/

      - name: Security checks
        run: |
          bandit -c pyproject.toml -r .

  tests:
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        python-version: ['3.9', '3.10', '3.11', '3.12']
    steps:
      - name: Check out repo
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install ".[dev]"
          pip install python-semantic-release

      - name: Automated tests with Pytest
        run: |
          pytest

  release:
    runs-on: ubuntu-latest
    needs:
      - tests
      - lint-static-checks
    steps:
      - name: Release
        run: echo "Release"
```
🧠 Purpose

This repository serves as a reference implementation of a modern DevOps pipeline for Python, ensuring:

    High code quality

    Automated security and static checks

    Fully automated versioning and deployment to PyPI

🏗️ Next Steps

    Add build status and PyPI badges

    Add code coverage reports using coverage.py and Codecov

    Optionally extend the workflow to include Docker image publishing

📦 License

This project is licensed under the MIT License — feel free to use and adapt it for your own CI/CD pipelines.
