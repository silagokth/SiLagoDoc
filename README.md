# SiLagoDoc

Documentation for SiLago Platform

## Site

[https://silago.eecs.kth.se/docs](https://silago.eecs.kth.se/docs)

## Guide

SiLagoDoc uses [zensical](zensical.org).
Zensical is a static site generator for markdown style files.
[Read more about markdown grammar](<https://www.mkdocs.org/user-guide/writing-your-docs/#writing-with-markdown>).

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
   zensical serve
   ```
