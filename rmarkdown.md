
# Rmarkdown

This documentation will eventually grow to a more complete resource of some "better" practices and examples for how to design and maintain an Rmd lab notebook.

##Good Practices

* When working 'live' on chunks, use a clean R session `rm(list = ls())` so you won't get anything different when you knit (eg. make sure no external code was run that wouldn't run when you knit)
* attach `sessionInfo()` at the bottom of every document!
  * better yet `devtools::session_info()` is a new(ish) function that gives more relevant information.
* If you have multiple files or additional complexities using a Makefile may be easier long term. In simple terms, a makefile is a script that re-builds your project so you can just source that script rather than manually clicking knit, etc.
* use chunk labels if possible


## Some 'gotchas' for knitting

* when knitted, each chunk is evaluated based on a working directory of the current file location of the Rmd document. 
        * This directory is reset after each chunk! So no setting in a higher level chunk and forgetting

## Extracting R Code

To *tangle* (extract program code), the function `purl()` will compile all R-code to a single .R file.

```
library(knitr)
purl("your-file.Rmd")
# results in your-file.R in the same directory
```

## Chunk Labels

Think of chunk labels as unique id's in a document. While they are used mainly for geration of external files, naming allows you to reference them elsewhere in your document. Automatically generated figures are also based on chunk-label names.

```
{r chunk_name, <additional options>}
```

## Global Options
global options can be modified at any point in your document and will affect all chunks below.

The syntax is `opts_chunk$set(<options-you-want,...>)`

## Digits of Output

* Control with `options(scipen = <#>, digits = <#>)`
        * `scipen` - controls when reported as scientific notation
        * digits = # digits to report

## Showing/Hiding Output Options

* `echo` - can take a TRUE/FALSE argument for whether to display the code as well as the output (default, TRUE) or just the output (FALSE) **or** can specify certain lines you would like to display
    - `echo=1:2` would display lines 1 and 2 only
    - note: line numbers are based on *expressions* rather than completed lines
        + see [here](http://stackoverflow.com/a/22274704/2773255) for more details
* `results`
    * `asis` - for when your output is already 'processed', eg when a function already gives you html or latex output. Tells knitr to not treat the code as markdown to be further processed but pass it directly on to the final output.
    * `hide` - like the opposite of `echo`, does not display output. Good if you want to show code, but not print the output.
* `warning/error/message` - whether to display warning/error/message(s).
* `split`
* `include` - whether to include the code chunk in your final document

## Figures

* Alignment - `fig.align = default=center/left/right`
* Path - `fig.path`
* height/width
  * `fig.height`
  * `fig.width`
  * `out.height`, `out.width`
  * `fig.retina` 
## Caching
* `cache = TRUE`

Do have some nice granular control options however

* update if version changes `version = R.version.string` 
* check to see if input file changes `<file>_name=file.info('<file>.csv')$mtime` and re-read data if newer
* check if other chunk updates `dependson='<chunk-name>'`
        * can also take integer chunk names `dependson = -1` would set dependency for chunk above

## Cross-Referencing

```
{r my-theme}
theme(axis.text.x = element_text(size = 18),
)
```

Then in later chunk
```
qplot(conc, Time, data = Theoph, color = Subject) + <<my-theme>>
```

TODO: flesh out and add additional material from knitr book

## Adding Tables

Knitr has a built in function `kable` that allows for easy creation of tables. 


```r
library(knitr)
kable(head(Theoph))
```



Subject      Wt   Dose   Time    conc
--------  -----  -----  -----  ------
1          79.6   4.02   0.00    0.74
1          79.6   4.02   0.25    2.84
1          79.6   4.02   0.57    6.57
1          79.6   4.02   1.12   10.50
1          79.6   4.02   2.02    9.66
1          79.6   4.02   3.82    8.58

It is worth checking out the documentation for kable via `?kable`

By default, the output is a markdown table, which makes printing to the console or evaluating the knitted markdown easy. `kable` also allows direct output into latex, html, pandoc, and rst via the `format` argument

One other highly useful argument is `digits`, which passes all values in numeric columns through the `round()` function before printing them out. This prevents analysis results to print all calculated digits.


```r
AUC_df <- data.frame(ID = 1:5, AUC = runif(5, 10, 100))
kable(AUC_df)
```



 ID    AUC
---  -----
  1   17.3
  2   85.1
  3   64.1
  4   24.1
  5   10.7

```r
kable(AUC_df, digits = 2)
```



 ID    AUC
---  -----
  1   17.3
  2   85.1
  3   64.1
  4   24.1
  5   10.7

QUESTION: can digits be specified for certain columns only?
