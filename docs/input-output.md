
# Input/Output

## Fast I/O

To preface this initial section, if you are working in a locked version of R, 
due to company policy, you may skip to the built-in section below.

Two packages provide much of the general I/O funtionality necessary:

1) `readr`
2) `data.table`

In addition, `PKPDmisc` offers wrappers that provide tailored input/output:

1) read_nonmem()
2) write_nonmem()
3) read_phx()   # for phx nlme datasets with a units column


## Built-in I/O

There are two primary reasons to introduce the built-in I/O functions as well:

1) `data.table` or `readr` will not always be available on your/collegues computers
2) the built in functions are designed to be incredibly robust, therefore will
often be able to handle edge cases that `readr` and `data.table::fread` fail on.


The principal way of reading/writing data in R `read.table` and `write.table`. Additionally, some "custom" functions with commonly used defaults are also available.

* `read.csv` is simply `read.table` with the default separator set to `,` and a couple other slight modifications

Another useful function is `source`. `source` allows us to read and execute R files automatically. 

The `read.table` function has some important arguments we can go through for later reference (note all the arguments are obviously the same for `read.csv`)

* `file` - name of file
* `header` - indicates if file has header line
* `sep` - how columns are separated
* `colClasses` - character vector with class of each column
* `nrows` - number of rows 
* `comment.char` - character string to indicate the comment character
* `stringsAsFactors` - how to handle strings (factor or simply strings)
* `skip` - how many lines to skip before starting to read
Lets also look at a couple relevant defaults for `read.table` and `read.csv`

`?read.table`

* unless specificied path is relative to current working directory
* header = FALSE
* stringsAsFactors = TRUE
* R will skip lines that begin with a # (comment.char = "#")

`?read.csv`

* header = TRUE
* sep = ","
* quote = "\""
* dec = "."
* fill = TRUE
* comment.char = ""


## Some tidbits

Assigning `colClasses` will significantly speed up the read.table process - this can be valuable for highly rich data.

A quick and dirty way of allowing R to detect and assign them for you is via:

```
initial <- read.table("data.txt", nrows = 50)
classes <- sapply(initial, class)
all_data <- read.table("data.txt", colclasses = classes)
```

## Output

Many of the output settings are similar to input settings. Again, your best bet is to spend a little bit of time checking out the documentation.

A couple values that are relevant and frequently useful:

* `quote = FALSE` - will keep column names from being output with quotes around them
* `na` - allows you to declare the string to use for missing values in data
* `row.names = FALSE` - don't output additional (any usually unnecessary for us) row names column
* `append = TRUE` - rather than overwriting a file w/ that name - it appends the results to the end of the file

[cookbook-R on writing text and output to files](http://www.cookbook-r.com/Data_input_and_output/Writing_text_and_output_from_analyses_to_a_file/)

### Your task

- you are given a dataframe with a header and units column
- create list with the column names and units that you can reference as necessary during analysis
- are there other ways you could store units to access them later? attributes? What are the risks?
- BONUS: write a new function write_phx that has both the header column as well as a units column below to make importing to phoenix and setting units easier. Write it such that you would not lose the type of each column when reading back into R

