# SiLagoDoc

Documentation for SiLago Platform

## Site

[https://silago.eecs.kth.se/docs](https://silago.eecs.kth.se/docs)

## Guide

SiLagoDoc uses [mkdocs](https://www.mkdocs.org/) with [Material theme](https://squidfunk.github.io/mkdocs-material/) as the framework. Makedocs is a python-based static site generator for markdown style files. You can read more about markdown grammar from [here](https://www.mkdocs.org/user-guide/writing-your-docs/#writing-with-markdown)

## Building

1. Create a Python venv

    ```bash
    python -m venv .venv
    source .venv/bin/activate
    ```

2. Install dependencies

    ```bash
    pip install -r requirements.txt
    ```

3. Compile the docs

    ```bash
    mkdocs serve
    ```
