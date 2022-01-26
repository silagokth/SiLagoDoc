# MkDocs Tutorial

This tutorial will teach you how to use [MkDocs](https://www.mkdocs.org/) to edit SiLagoDoc.

## What is MkDocs?

MkDocs is a fast, simple and downright gorgeous static site generator that's geared towards building project documentation. Documentation source files are written in Markdown, and configured with a single YAML configuration file.

MkDocs is based on [python](https://www.python.org/). The SiLagoDoc uses **python 3**.

## How to install MkDocs?

Like all python packages, you can install MkDocs and its theme via **pip**.

```sh
pip install mkdocs mkdocs-material
```

## How to use MkDocs?

### Clone SiLagoDoc repo

Use the following command:

```sh
git clone git@github.com:silagokth/SiLagoDoc.git
```

### Check the modification

!!! Note
    The render result is just on your local machine. It hasn't been commited to the server yet.

Use the the follwing command:

```sh
cd SiLagoDoc
mkdocs serve
```
Now you can view the SiLagoDoc on your browser via the url: [http://127.0.0.1:8000](http://127.0.0.1:8000). The site will be automatically refreshed when you save your changes.


### Automatically deploy the site

By simply commit your change to the repository, the site will be automatically updated. It might take several minutes.

### Manually Build and deploy the site

!!! Warning
    SiLagoDoc has been configured with continous integration. You **should not** use this step to manually deploy the site. You should instead directly commit your source code to github and let it automatically generate the site.

Once you are satisfied with the modification, you can build the site using the command:

```sh
mkdocs build
```

You will notice that a folder called ***site*** has been created. This site can then be deployed to the server.

SiLagoDoc uses [github pages](https://pages.github.com/) to host the site. So you can use the following command to deploy:

```sh
mkdocs gh-deploy
```

## How to write documentation?

### Add a page

You can add a documentation page in */docs*. Some files already exist under the directory. There are three folders: */docs/About*, */docs/Guideline*, and */docs/Docs*. Choose the correct folder to add your page. The documentation page uses [Markdown](https://www.markdownguide.org/basic-syntax/) format. The page file should have appendix of ".md".

Once a page is added, you should also update the configuration file to make it available on the navagation bar. The configuation file is: */mkdocs.yaml*.

### Edit a page

You can edit a page and commit the change to github repo. Please see [Markdown syntax](https://www.markdownguide.org/basic-syntax/).

You can also directly edit a page through the website. Each page has a pencil icon on the top-right corner. You can click the icon, and you shall be directed to the github source code editing interface. You can just edit the file and save the change. Since SiLagoDoc has the continuous integration, your change will be automatically build and deploy to the website. It might take several minuts to refresh the site.

### Delete a page

Just remove the Markdown file and remove the corresponding entry in */mkdocs.yaml*.

## Theme and advanced features

SiLagoDoc uses **material** theme for MkDocs. Besides the looks, the material theme also provides some extra features.

The complete list of supported extensions can be found [Here](https://squidfunk.github.io/mkdocs-material/setup/extensions/python-markdown-extensions/).