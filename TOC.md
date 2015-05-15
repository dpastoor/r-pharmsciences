/***
anything between the multiline comment block (javascript style) will be ignored
by the script that creates the SUMMARY.md file

Format should be:
#' Settings
chapter-tags: <deepest h-level> 
#' Table of Contents
* [Chapter name](path-to-rmd.Rmd)

script will automatically add chapters
***/
#' Settings:
chapter-tags: h2
#' Table of Contents
* [Core R](core-r/core-r.Rmd)
* [Thinking in R](thinking-in-r/thinking-in-r.Rmd)
* [Data Input/Output](input-output/input-output.Rmd)
* [Intro to Data Manipulation](intro-data-manipulation/intro-data-manipulation.Rmd)
* [Functions](function-writing/function-writing.Rmd)
* [Apply Functions](apply-functions/apply-functions.Rmd)
* [Reproducible Research](reproducible-research/reproducible-research.Rmd)
* [Rmarkdown](rmarkdown/rmarkdown.Rmd)