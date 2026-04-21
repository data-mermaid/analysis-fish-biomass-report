# Fish Biomass Report Card from MERMAID Data

R/Quarto template that pulls coral reef fishbelt transect data from [MERMAID](https://datamermaid.org) and produces report-ready visualizations for fish biomass reporting. Designed to work with any MERMAID project — just change the project name and thresholds.

## Overview

This repository provides a single, configurable Quarto document that generates fish biomass report card figures from MERMAID sample event data. It authenticates with the MERMAID API via the [`mermaidr`](https://github.com/data-mermaid/mermaidr) R package and searches for projects by name using `mermaid_search_projects()`.

All project-specific settings (project name, thresholds, site grouping) are controlled through a Configuration section at the top of the document, so the code does not need to be edited elsewhere to adapt it to a new project.

For data quality checks on fishbelt data (observer comparisons, outlier detection, family diversity, biomass composition), see the companion repository: [analysis-data-checks-fishbelt](https://github.com/data-mermaid/analysis-data-checks-fishbelt).

## Analyses

### Fish biomass histogram with stoplight thresholds

An interactive (plotly) histogram of site-level mean fish biomass, color-coded into three bins — red (below yellow threshold), yellow (between thresholds), and green (above green threshold). Dotted vertical lines mark the threshold boundaries. Thresholds are configurable (default: 500 and 1000 kg/ha). Based on the [MERMAID Explore](https://datamermaid.org/explore) visualizations.

### Stacked bar plot by trophic group

Horizontal stacked bars for each site showing biomass contributions from eight trophic groups (planktivore, herbivore macroalgae, herbivore detritivore, invertivore sessile, invertivore mobile, omnivore, piscivore, other). Colors match the MERMAID dashboard. Sites can optionally be grouped (e.g., by community) and faceted, with configurable group ordering and merging. The code auto-detects whether grouping is meaningful (i.e., at least one group contains more than one site) and falls back to a single ungrouped plot if not. Axis scale and breaks adjust dynamically based on the data range. When a site has multiple sample events, the label automatically appends the survey date (and depth, if needed) to keep each bar distinct. Based on code originally created for a [Madagascar report card](https://datamermaid.org/analyses/madagascar-report-card-benthic-cover-fish-biomass).

### Stacked bar plot by food fish family (optional)

Same layout as the trophic group plot but broken down by fish family. Because which families are considered "food fish" varies by region, this section is set to `eval: false` by default and requires the user to define their own family list. Includes a Solomon Islands example list as a starting point.

## Configuration options

All settings are in the first code chunk of `analysis/fish_biomass_report.qmd`:

| Variable | Description | Default |
|---|---|---|
| `project_name` | MERMAID project name to search for | `"SERF2_AlanFriedlander_Niue"` |
| `yellow_threshold` | Lower stoplight boundary (kg/ha) | `500` |
| `green_threshold` | Upper stoplight boundary (kg/ha) | `1000` |
| `use_site_grouping` | Group sites by name prefix | `TRUE` |
| `custom_group_order` | Manual group ordering (or `NULL` for biomass-based) | `NULL` |
| `group_merges` | Named list to merge group names (or `NULL`) | `NULL` |

## Repository structure

```
├── analysis/
│   ├── _quarto.yml                   # Quarto project config (output → docs/)
│   ├── index.qmd                     # Landing page with links
│   ├── fish_biomass_report.qmd       # Main analysis document
│   ├── footer.html                   # "Powered by MERMAID" footer
│   └── 2021MERMAIDLogoBlueTransp.png # MERMAID logo
├── docs/                             # Rendered HTML (for GitHub Pages)
├── plots/                            # Saved figures (SVG)
├── analysis-fish-biomass-report.Rproj
├── LICENSE                           # AGPL-3.0
└── README.md
```

## Requirements

- R (≥ 4.1)
- Quarto (for rendering `.qmd` files)
- R packages: `mermaidr`, `tidyverse`, `plotly`, `htmlwidgets`, `DT`, `ggplot2`, `ggpubr`
- For the optional food fish family plot: `colorspace`
- A MERMAID account with access to at least one project matching your project name

## Usage

1. Open the `.Rproj` file in RStudio.
2. Edit the Configuration section in `analysis/fish_biomass_report.qmd` to set your project name and thresholds.
3. Render the document: `quarto render analysis/fish_biomass_report.qmd`
4. On first run, `mermaidr::mermaid_auth()` will open a browser window for OAuth authentication.
5. Output HTML is self-contained and saved to `docs/` for GitHub Pages hosting.

## License

[GNU Affero General Public License v3.0](LICENSE)
