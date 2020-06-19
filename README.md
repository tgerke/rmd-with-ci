Document editing with R Markdown + GitHub Actions
=====

This repository holds a template for establishing a CI workflow towards collaborative document editing in R Markdown.
The goal is that edits to a single `.Rmd` file will trigger automatic builds of `.pdf`, `.docx`, `.html`, and `.md` versions of the same document via [rmarkdown](https://github.com/rstudio/rmarkdown). 

How this was set up
-----

Let's first create the skeleton of an `.Rmd` document that we wish to render.

```r
skeleton <- glue::glue('
---
title: "My document"
output: 
  github_document: default
  word_document: default
  html_document: default
  pdf_document: default
---

Introduction
---

Some highly informative text.

')

dir.create(here::here("my-document"))
cat(skeleton, file = here::here("my-document", "my-document.Rmd"))
```

The quickest way to get a GitHub Actions template for rendering 
a document is to grab this example action from 
[r-lib](https://github.com/r-lib/actions/tree/master/examples) 
which renders generic readme file. The below line initializes the 
file and renames it for editing. 

```r
# create yaml file
usethis::use_github_action("render-readme.yaml")

# rename to render a generic doc
fs::file_move(
  here::here(".github/workflows/render-readme.yaml"),
  here::here(".github/workflows/render-doc.yaml")
)
```

We then edit the `.yaml` in session:
```r
usethis::edit_file('.github/workflows/render-doc.yaml')
```

You can view the resulting file within the repo, 
but for ease of reading: 
```yaml
on:
  push:
    paths:
      - my-document/my-document.Rmd

name: Render my document

jobs:
  render:
    name: Render my document
    runs-on: macOS-latest
    steps:
      - uses: actions/checkout@v2
      - uses: r-lib/actions/setup-r@v1
      - uses: r-lib/actions/setup-pandoc@v1
      - uses: r-lib/actions/setup-tinytex@v1
      - name: Install rmarkdown
        run: Rscript -e 'install.packages("rmarkdown")'
      - name: Install tinytex in session (necessary for newer OS X)
        run: Rscript -e 'tinytex::install_tinytex()'
      - name: Render my document to all types
        run: Rscript -e 'rmarkdown::render("my-document/my-document.Rmd", output_format = "all")'
      - name: Commit results
        run: |
          git add my-document/my-document*
          git commit -m 'Re-build my-document' || echo "No changes to commit"
          git push origin || echo "No changes to commit"
```

