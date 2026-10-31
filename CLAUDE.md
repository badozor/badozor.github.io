# CLAUDE.md

Notes for Claude when working on this repository. Written 2026-10-31.

## What this is

Personal academic website of Pierre Bady (biostatistician / bioinformatician).
Live at https://badozor.github.io (GitHub Pages, repo `badozor/badozor.github.io`, branch `main`).

It is an **R Markdown website** (`rmarkdown::render_site`). Each page has an `.Rmd` source and a generated `.html` file. Both are committed. GitHub Pages serves the committed HTML directly. There is no CI build.

## Folder structure

```
.
├── _site.yml                 Site config: navbar, theme, CSS, output_dir "."
├── styles.css                Custom CSS on top of the Bootstrap "cosmo" theme
├── badozor.github.io.Rproj   RStudio project (BuildType: Website, 2-space indent, UTF-8)
├── index.Rmd / .html         Home: affiliations, short CV, ORCID, contact
├── builds.Rmd / .html        Software: R packages table, Python packages table, other software
├── ressources.Rmd / .html    Resources: docs for mgmtstp27, efitools, ggade4 notes
├── publications.Rmd / .html  Publications rendered from data/pbady.bib
├── datasets.Rmd / .html      Datasets: GEO table and Zenodo table
├── misc.Rmd / .html          Quotes (French and English)
├── resources.html            Orphan: old render (header-attrs-2.12), no .Rmd, not in navbar
├── data/
│   ├── rpackages.csv         R packages table (feeds builds.Rmd)
│   ├── pythonpackages.csv    Python packages table (feeds builds.Rmd)
│   ├── geo_datasets.csv      GEO datasets, ";" separated (feeds datasets.Rmd)
│   ├── geo_datasets.txt      Raw GEO search export, not used by any page
│   ├── zenodo_datasets.csv   Zenodo datasets, ";" separated (feeds datasets.Rmd)
│   ├── pbady.bib             BibTeX of all publications (feeds publications.Rmd)
│   └── efitools-1.2.rar      Downloadable software, linked from builds and ressources
├── image/                    Rlogo.png, Rlogo.svg, Python-logo.png (section headers on builds)
├── site_libs/                JS/CSS dependencies written by rmarkdown. Do not edit by hand.
├── README.md                 One line with the site URL (listed in .gitignore but tracked)
└── LICENSE
```

## Pages (navbar order, from `_site.yml`)

| Navbar     | File               | Icon           | Content |
|------------|--------------------|----------------|---------|
| Home       | index.html         | fa-home        | Affiliations, short CV (2000 to present), ORCID 0000-0003-1398-4689, contact emails written as "(at)" |
| Builds     | builds.html        | fa-gears       | kable tables from the two package CSVs, plus "Other software packages" (efitools 1.2) |
| Ressources | ressources.html    | fa-toolbox     | Static links: mgmtstp27 PDFs on GitHub, efitools report and software on ResearchGate |
| Publications | publications.html | fa-file-pdf   | Loops over `pbady.bib`: numbered "Articles" first, then numbered "Other publications" |
| Datasets   | datasets.html      | fa-hard-drive  | GEO table (Accession turned into a GEO link by `addGeoLink()`), Zenodo table |
| Misc       | misc.html          | fa-paperclip   | Italic quotes with attribution |
| GitHub     | external           | fa-github      | https://github.com/badozor |

Note: the page is spelled **"Ressources"** (French spelling) in the navbar, file name and title. Keep it.

## Design choices

- Theme: Bootstrap 3 `cosmo` (`html_document`), code highlight `textmate`.
- Navbar title: "Pierre BADY".
- Colors in `styles.css`:
  - Navbar background `#228B22` (forest green); `.navbar-inverse` variants `#222` with `#9d9d9d` text.
  - Links `#55A649` (green), hover `#A74956` (muted red), no focus outline.
- Fonts: none custom. Cosmo's defaults (Source Sans Pro) are used.
- Icons: Font Awesome 6.4.2 (navbar) via rmarkdown.
- Section headings are H1 in bold: `# **Title**`.
- Spacing between blocks uses raw `<br>` lines, often two `<br>` after a table.
- Tables: `knitr::kable(df, "html") %>% kable_styling(full_width = TRUE)` with `library(kableExtra)`. Chunks are `echo=FALSE`, tables in `results='asis'` chunks.
- Logos in builds headings: `![](image/Rlogo.png){width=5%}`.
- Page bodies are in English. Misc quotes mix French and English.

## Data file conventions

- `rpackages.csv`, `pythonpackages.csv`: comma separated, every field in double quotes, columns `"name","description","link","version"`.
  - `link` is Markdown: `[name](https://github.com/badozor/name)`.
  - `description`: one English sentence, in the tone of existing rows.
  - `version`: from DESCRIPTION / `__version__`; otherwise `"in construction"`.
  - Each file ends with a filler row `"zzz","  ","  ","0.1"`. Keep it last. Insert new rows just before it.
- `geo_datasets.csv`: `;` separated, columns `Accession;Title;Type;Sample`.
- `zenodo_datasets.csv`: `;` separated, columns `Title;DOI;Type;Sample`.
- `pbady.bib`: standard BibTeX, keys like `bady2004`. Read with `bibtex::read.bib`. The `bibtype` decides Articles vs Other.

## How to build (important)

- Render one page: `Rscript -e 'rmarkdown::render_site("builds.Rmd", quiet=TRUE)'`. Render all: `rmarkdown::render_site()`.
- The committed pages were rendered with **rmarkdown 2.27** and **jquerylib 0.1.4**. Their HTML references `site_libs/header-attrs-2.27` and `site_libs/jquery-3.6.0`.
- Other versions (for example Ubuntu's rmarkdown 2.25, jquery 3.6.1) rewrite `site_libs` and break the other pages. If CRAN is unreachable, install those exact versions from GitHub tags (`rstudio/rmarkdown` v2.27, `rstudio/jquerylib` v0.1.4) into a local library and render with `R_LIBS` pointing to it.
- After rendering, check `git status`: only the edited `.Rmd`, its `.html` and data files should change. If `site_libs` changed or a new folder like `site_libs/jquery-3.6.1` appeared, run `git checkout -- site_libs`, delete the new folder, and fix the environment.
- R packages needed: `rmarkdown`, `knitr`, `kableExtra`, `bibtex`.
- Old `header-attrs-*` and `jquery-1.11.3` folders in `site_libs` are leftovers from past renders. Leave them.

## Owner's preferences

- Follow the existing programming style. Change pages by editing the `.Rmd` and the CSV/BibTeX data, then **re-knit with R**. Do not hand-edit generated HTML as the final result.
- Never invent descriptions, versions or dates. Take them from the repositories (README, DESCRIPTION, pyproject, `__init__.py`). Flag anything uncertain.
- Never remove a row from the package tables automatically. Report a vanished repo instead.
- Commit author: `badozor <pierre.bady@gmail.com>`.
- Communicate with Pierre in French, informal "tu", short sentences, no em dashes, no corporate filler.

## Automation in place

- A weekly scheduled task "MAJ page Builds GitHub" (Mondays 08:37 Europe/Zurich) compares Pierre's GitHub repos with the two package CSVs. It adds new rows, re-knits `builds.Rmd` with rmarkdown 2.27, and opens a **pull request** (branch `update-builds-YYYY-MM-DD`) rather than pushing to `main`.
- Account-level GitHub API calls (`users/badozor/repos`) are blocked in Claude sessions. Repo listing goes through the session's repo-listing tool instead.

## Known loose ends (not fixed, for awareness)

- `index.Rmd` affiliations and CV still say "Oct 2009-presently" at CHUV / SIB.
- `resources.html` is an orphan duplicate of the Ressources page.
- `ressources.Rmd` has two placeholder sections with no link ("ggade4 (see R package PBmisc)", "Tex document").
- `styles.css` has two rules missing a `;` after `background-color: #222`, so the following `border-color` is ignored.
