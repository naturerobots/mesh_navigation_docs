# Mesh Navigation Docs

These docs are built with [MkDocs](https://mkdocs.org) and published to [naturerobots.github.io/mesh_navigation_docs](https://naturerobots.github.io/mesh_navigation_docs)

## Build locally

### Install mkdocs

#### Using pip

```
sudo apt install python3-pip
pip3 install "mkdocs>=1.6" mkdocs-material
```

*Note: you may need to adapt your path `export PATH=$PATH:/home/<USER>/.local/bin` to access mkdocs binary*

#### Using conda

After downloading go into the folder of this repository.

```bash
conda env create -f environment.yml
```

Will create an conda environment named `mesh_navigation_docs`. Enable it by calling

```bash
conda activate mesh_navigation_docs
```

### Run mkdocs

#### Build Static Files

```
mkdocs build --strict
```

#### Live reloading server on :8000

```
mkdocs serve --strict
```

## Deploy to naturerobots.github.io/mesh_navigation_docs

Anything pushed to branch `deploy` will trigger a Github Action that builds the website and updates [naturerobots.github.io/mesh_navigation_docs](https://naturerobots.github.io/mesh_navigation_docs)

## Contributions

You are welcome to contribute to the docs of [Mesh Navigation](https://github.com/naturerobots/mesh_navigation)! Thorough and clear documentation is essential. You can help us by correcting mistakes, improving content, or adding examples that facilitate user navigation and usage of the project. Please submit any documentation-related issues here. If you're making fixes or adding examples, don’t hesitate to submit a pull request afterward!

### PR workflow

How to contribute to this documentation via pull requests:

1. Fork this repository.
2. Make changes on your forked repository.
3. Check locally on your machine if mkdocs is able to compile your changes ([instructions](https://github.com/uos/mesh_navigation_docs)).
3. Go to Github and click "Pull Request", select this repository's "main" branch as target.
4. If you added new content, please provide a brief explanation of why you believe it is beneficial for the documentation.
5. Send PR.
