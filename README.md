Document editing with R Markdown + GitHub Actions
=====

This repository holds a template for establishing a CI workflow towards collaborative document editing in R Markdown.
The goal is that edits to a single `.Rmd` file will trigger automatic builds of `.pdf`, `.docx`, `.html`, and `.md` versions of the same document via `rmarkdown`. 

How this was set up
-----

The example action to render a generic readme file from
[r-lib](https://github.com/r-lib/actions/tree/master/examples) 
was initialized and renamed for editing. 

```r
# create yaml file
usethis::use_github_action("render-readme.yaml")

# rename to render a generic doc
fs::file_move(
  here::here(".github/workflows/render-readme.yaml"),
  here::here(".github/workflows/render-doc.yaml")
)
```

```r
usethis::edit_file('.github/workflows/render-doc.yaml')
```

