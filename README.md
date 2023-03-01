---
title: "SIH Quarto Training template"
---

# IMPORTANT!

This repository can serve as a template for making materials from:

- Jupyter notebooks aka `.ipynb` files
- R content - `.qmd` files, which mix R (and Python via reticulate, if needed) and md sections
- Plain old `.md` files

(see instructions below)

To:

- Support this cross-language functionality
- Keep the repo as lean as possible
- Ensure as little unexpected behaviour as possible, please:

1. Do not under any circumstances commit to the main branch without review by someone else
2. Fork the repository, make and test changes, THEN put in a pull request
3. Please do not add GitHub actions or other "enhacements" that only work for your favourite programming language or workflow, and/or require a lot of extra files to work.

While GitHub actions seem like an amazing enhancement, we often develop materials that use custom libraries, renv or conda environments, docker builds etc.

So if you'd like to use actions: PLEASE:

1. Create your custom training workshop repository for your content
2. Delete what you don't need from this template
3. Create your custom actions script
4. Add a note to the bottom of this readme linking to your actions page, so others can use it as an example when creating training using a similar combination of environments and language - THIS REPO IS GENERIC BY DESIGN.



# File location

- We recommend storing all notebooks (.ipynb) or R files (as .qmd or .rmd) in the `notebooks` folder.

## How to use

All languages/sources:

1. Use the big green button "Use this template" this repository
2. Edit `index.qmd` to change the main landing page. This is basically a markdown file.
3. Edit or create `setup.qmd` to change the Setup instruction pages. Same - basically a md file.
4. Edit `_quarto.yml` to change the dropdown menu options.
5. Add additional `*.md` files to the root dir to have them converted to html files (and add them to `_quarto.yml` to make them navigable), if you'd like.
6. Edit this Readme in your fork to reflect the content of your workshop.


If you want to use the command line instead of VSCode/RStudio (as described below), run the below commands (after activating the correct Python environment, if needed) 

```
quarto render
# First time you create the file, add them to be tracked by github, e.g.
git add docs/*
git commit -am "your comments"
git push
```

You can browse the result locally by exploring the html files created (note: sometimes figures display locally but not on web and the other way around too.)

### For Python - main "content" in `ipynb`

- You will need to have Jupyter and Quarto installed to convert the notebooks and render them for the web.
- [Recommended, not essential] Use VSCode's [Quarto Extension](https://quarto.org/docs/tools/vscode.html) to render the project (recommended b/c it's easier/nice).

1. Delete `notebooks/01b-exampleRcontent.qmd` and `01c-exampleMDcontent.md`
2. Create notebooks in the `notebooks` folder (for example `notebooks/1_cont.ipynb`). Have a look at what the syntax for Challenges, Objectives, Key Points and Questions is supported in `01a-fundamentals.ipynb`, and use similar syntax across other notebooks where needed.
3. Execute all the cells in your Jupyter notebook(s) until you're happy with the output/
4. Add links to your content to the navigation configuration in `_quarto.yml`. For example, to link to the rendered page for `notebooks/1_cont.ipynb`, add a link to `notebooks/1_cont.html` in `_quarto.yml`

Note:

The building from Jupyter notebooks will NOT re-render all of the notebooks unless you use the `quarto render notebook.ipynb --execute` command

### For R - main "content" in `qmd`

- `.qmd` is called "Quarto Markdown", and basically works just like Rmd.
- If using R, you will need rmarkdown, xml2 and X to have the notebooks generate properly and link out to the documentation, as specified in the `_quarto.yml` file.
- Building from R will by default re-render all of the outputs.


1. Delete `notebooks/01a-fundamentals.ipynb`, `environment.yml` and `01c-exampleMDcontent.md`
2. Create notebooks in the `notebooks` folder (for example `notebooks/1_cont.qmd`). Have a look at what the syntax for Challenges, Objectives, Key Points and Questions is supported in `01b-exampleRcontent.qmd`, and use similar syntax across other .qmd files where needed.
3. Add links to your content to the navigation configuration in `_quarto.yml`. For example, to link to the rendered page for `notebooks/1_cont.qmd`, add a link to `notebooks/1_cont.html` in `_quarto.yml`
4. Type `quarto render` in the Terminal in RStudio (not the R command line, the Terminal tab!) - or use the buttons.

### For everything else or just if you feel like it - main "content" in `md`

1. Delete `notebooks/01a-fundamentals.ipynb`,`01b-exampleRcontent.qmd` and `environment.yml` 
2. Create files in the `notebooks` folder (for example `notebooks/1_cont.md`). Have a look at what the syntax for Challenges, Objectives, Key Points and Questions is supported in `01c-exampleMDcontent.md`, and use similar syntax across other .md files where needed.
3. Add links to your content to the navigation configuration in `_quarto.yml`. For example, to link to the rendered page for `notebooks/1_cont.md`, add a link to `notebooks/1_cont.html` in `_quarto.yml`
4. Type `quarto render` in the terminal - or use VSCode's "Render Quarto project' command using the command pallette instead.


***

# Examples of template use, with GitHub actions