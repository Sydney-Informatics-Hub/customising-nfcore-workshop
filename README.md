# Australian BioCommons: customising nf-core workshop series

Workshop title: introduciton to nf-core and customising it for your research.

## How to use

1. Edit `index.qmd` to change the main landing page. This is basically a markdown file.
2. Edit or create `setup.qmd` to change the Setup instruction pages. Same - basically a md file.
3. Edit `_quarto.yml` to change the dropdown menu options.
4. Add additional `*.md` files to the root dir to have them converted to html files (and add them to `_quarto.yml` to make them navigable), if you'd like.

If you want to use the command line instead of VSCode/RStudio (as described below), run the below commands (after activating the correct Python environment, if needed) 

```
quarto render
# First time you create the file, add them to be tracked by github, e.g.
git add docs/*
git commit -am "your comments"
git push
```

You can browse the result locally by exploring the html files created (note: sometimes figures display locally but not on web and the other way around too.)

### For main "content" in `md`/`qmd`

1. Create files in the `notebooks` folder (for example `notebooks/1_cont.md`). 
Have a look at what the syntax for Challenges, Objectives, Key Points and Questions is supported in `0.0.0_template.md`, and use similar syntax across other .md files where needed.
2. Add links to your content to the navigation configuration in `_quarto.yml`. For example, to link to the rendered page for `notebooks/1_cont.md`, add a link to `notebooks/1_cont.html` in `_quarto.yml`
3. Type `quarto render` in the terminal - or use VSCode's 'Render Quarto project' command using the command pallette instead.
