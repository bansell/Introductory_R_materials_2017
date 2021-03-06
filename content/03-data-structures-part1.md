


# Data structures

<!--sec data-title="Learning Objectives" data-id="obj" data-show=true data-collapse=false ces-->

- To be aware of the different types of data
- To begin exploring the data.frame, and understand how it's related to vectors, factors and lists
- To be able to ask questions from R about the type, class, and structure of an object.

<!--endsec-->

<br>

---

**Table of Contents**

<!-- toc -->

<br>

---

One of R's most powerful features is its ability to deal with tabular data - like what you might already have in a spreadsheet or a CSV. Let's start by making a toy dataset in your `data/` directory, called `feline-data.csv`:


~~~sourcecode
coat,weight,likes_string
calico,2.1,TRUE
black,5.0,FALSE
tabby,3.2,TRUE
~~~



We can load this into R via the following:


~~~sourcecode
cats <- read.csv(file = "../data/feline-data.csv")
cats
~~~



~~~output
    coat weight likes_string
1 calico    2.1         TRUE
2  black    5.0        FALSE
3  tabby    3.2         TRUE

~~~

We can begin exploring our dataset right away, pulling out columns and rows via the following:


~~~sourcecode
cats$weight
~~~



~~~output
[1] 2.1 5.0 3.2

~~~



~~~sourcecode
cats$weight[1]
~~~



~~~output
[1] 2.1

~~~



~~~sourcecode
cats$weight[2]
~~~



~~~output
[1] 5

~~~



~~~sourcecode
cats$weight[1] + cats$weight[2]
~~~



~~~output
[1] 7.1

~~~

But what about


~~~sourcecode
cats$weight[1] + cats$coat[2]
~~~



~~~err
Warning in Ops.factor(cats$weight[1], cats$coat[2]): '+' not meaningful for
factors

~~~



~~~output
[1] NA

~~~

Understanding what happened here is key to successfully analyzing data in R.

<br>

---

## Data Types

If you guessed that the last command will return an error because 2.1 plus black is nonsense, you're right - and you already have some intuition for an important concept in programming called *data types*. We can ask what type of data something is:


~~~sourcecode
typeof(cats$weight[1])
~~~



~~~output
[1] "double"

~~~

There are 5 main types: doubles, integers, complex, logical and character.

```
typeof(3.14)
typeof(1L)
typeof(1+1i)
typeof(TRUE)
typeof('banana')
```

Note the `L` suffix to insist that a number is an integer. No matter how complicated our analyses become, all data in R is interpreted as one of these basic data types. This strictness has some really important consequences. Go back to your text editor and add add this line to feline-data.csv:


~~~sourcecode
tabby,2.3 or 2.4,TRUE
~~~



Reload your cats data like before, and check what type of data we find in the `weight` column:


~~~sourcecode
cats <- read.csv(file="../data/feline-data.csv")
typeof(cats$weight[1])
~~~



~~~output
[1] "integer"

~~~

Oh no, our weights aren't the double type anymore! If we try to do the same math we did on them before, we run into trouble:


~~~sourcecode
cats$weight[1] + cats$weight[2]
~~~



~~~err
Warning in Ops.factor(cats$weight[1], cats$weight[2]): '+' not meaningful
for factors

~~~



~~~output
[1] NA

~~~

What happened? When R reads a csv into one of these tables, it insists that everything in a column be the same basic type; if it can't understand *everything* in the column as a double, then *nobody* in the column gets to be a double. The table that R loaded our cats data into is something called a *data.frame*, and it is our first example of something called a *data structure* - things that R knows how to build out of the basic data types. In order to successfully use our data in R, we need to understand what these basic data structures are, and how they behave. For now, let's remove that extra line from our cats data and reload it, while we investigate this behavior further:

feline-data.csv:

```
coat,weight,likes_string
calico,2.1,TRUE
black,5.0,FALSE
tabby,3.2,TRUE
```



And back in RStudio:

```
cats <- read.csv(file="../data/feline-data.csv")
```

<br>

---

## Vectors & Type Coercion

To better understand the behavior we just saw, let's meet another of the data structures: the *vector*.


~~~sourcecode
my_vector <- vector(length=3)
my_vector
~~~



~~~output
[1] FALSE FALSE FALSE

~~~

Just like you might be familiar with from vectors elsewhere, a vector in R is essentially an ordered list of things, with the special condition that *everything in the vector must be the same basic data type*. If you don't choose the datatype, it'll default to `logical`; or, you can declare an empty vector of whatever type you like.


~~~sourcecode
another_vector <- vector(mode='character', length=3)
another_vector
~~~



~~~output
[1] "" "" ""

~~~

You can check if something is a vector:


~~~sourcecode
str(another_vector)
~~~



~~~output
 chr [1:3] "" "" ""

~~~

The somewhat cryptic output from this command indicates the basic data type found in this vector; the number of things in the vector; and a few examples of what's actually in the vector. If we similarly do


~~~sourcecode
str(cats$weight)
~~~



~~~output
 num [1:3] 2.1 5 3.2

~~~

we see that that's a vector, too - *the columns of data we load into R data.frames are all vectors*, and that's the root of why R forces everything in a column to be the same basic data type.

<!--sec data-title="Discussion 1" data-id="disc1" data-show=true data-collapse=false ces-->

Why is R so opinionated about what we put in our columns of data?
How does this help us?

<!--endsec-->

You can also make vectors with explicit contents with the concatenate function:


~~~sourcecode
concat_vector <- c(2,6,3)
concat_vector
~~~



~~~output
[1] 2 6 3

~~~

Given what we've learned so far, what do you think the following will produce?


~~~sourcecode
quiz_vector <- c(2,6,'3')
~~~

This is something called *type coercion*, and it is the source of many surprises and the reason why we need to be aware of the basic data types and how R will interpret them. Consider:


~~~sourcecode
coercion_vector <- c('a', TRUE)
coercion_vector
~~~



~~~output
[1] "a"    "TRUE"

~~~



~~~sourcecode
another_coercion_vector <- c(0, TRUE)
another_coercion_vector
~~~



~~~output
[1] 0 1

~~~

The coercion rules go: `logical` -> `integer` -> `numeric` -> `complex` -> `character`. You can try to force coercion against this flow using the `as.` functions:


~~~sourcecode
character_vector_example <- c('0','2','4')
character_vector_example
~~~



~~~output
[1] "0" "2" "4"

~~~



~~~sourcecode
character_coerced_to_numeric <- as.numeric(character_vector_example)
character_coerced_to_numeric
~~~



~~~output
[1] 0 2 4

~~~



~~~sourcecode
numeric_coerced_to_logical <- as.logical(character_coerced_to_numeric)
numeric_coerced_to_logical
~~~



~~~output
[1] FALSE  TRUE  TRUE

~~~

As you can see, some surprising things can happen when R forces one basic data type into another! Nitty-gritty of type coercion aside, the point is: if your data doesn't look like what you thought it was going to look like, type coercion may well be to blame; make sure everything is the same type in your vectors and your columns of data.frames, or you will get nasty surprises!

Concatenate will also append things to an existing vector:


~~~sourcecode
ab_vector <- c('a', 'b')
ab_vector
~~~



~~~output
[1] "a" "b"

~~~



~~~sourcecode
concat_example <- c(ab_vector, 'SWC')
concat_example
~~~



~~~output
[1] "a"   "b"   "SWC"

~~~

You can also make series of numbers:


~~~sourcecode
mySeries <- 1:10
mySeries
~~~



~~~output
 [1]  1  2  3  4  5  6  7  8  9 10

~~~



~~~sourcecode
seq(10)
~~~



~~~output
 [1]  1  2  3  4  5  6  7  8  9 10

~~~



~~~sourcecode
seq(1,10, by=0.1)
~~~



~~~output
 [1]  1.0  1.1  1.2  1.3  1.4  1.5  1.6  1.7  1.8  1.9  2.0  2.1  2.2  2.3
[15]  2.4  2.5  2.6  2.7  2.8  2.9  3.0  3.1  3.2  3.3  3.4  3.5  3.6  3.7
[29]  3.8  3.9  4.0  4.1  4.2  4.3  4.4  4.5  4.6  4.7  4.8  4.9  5.0  5.1
[43]  5.2  5.3  5.4  5.5  5.6  5.7  5.8  5.9  6.0  6.1  6.2  6.3  6.4  6.5
[57]  6.6  6.7  6.8  6.9  7.0  7.1  7.2  7.3  7.4  7.5  7.6  7.7  7.8  7.9
[71]  8.0  8.1  8.2  8.3  8.4  8.5  8.6  8.7  8.8  8.9  9.0  9.1  9.2  9.3
[85]  9.4  9.5  9.6  9.7  9.8  9.9 10.0

~~~

In addition to asking for elements of a vector with the square bracket notation, we can ask a few other questions about vectors:


~~~sourcecode
sequence_example <- seq(10)
head(sequence_example, n=2)
~~~



~~~output
[1] 1 2

~~~



~~~sourcecode
tail(sequence_example, n=4)
~~~



~~~output
[1]  7  8  9 10

~~~



~~~sourcecode
length(sequence_example)
~~~



~~~output
[1] 10

~~~

Finally, you can give names to elements in your vector, and ask for them that way:


~~~sourcecode
names_example <- 5:8
names(names_example) <- c("a", "b", "c", "d")
names_example
~~~



~~~output
a b c d 
5 6 7 8 

~~~



~~~sourcecode
names_example['b']
~~~



~~~output
b 
6 

~~~

<!--sec data-title="Challenge 1" data-id="ch1" data-show=true data-collapse=false ces-->

Start by making a vector with the numbers 11 to 20. Then use the functions we just learned to extract the 3rd through 5th element in that vector into a new vector; name the elements in that new vector 'S', 'W', 'C'.

<!--endsec-->

<br>

---

## Factors

We said that columns in data.frames were vectors:


~~~sourcecode
str(cats$weight)
~~~



~~~output
 num [1:3] 2.1 5 3.2

~~~



~~~sourcecode
str(cats$likes_string)
~~~



~~~output
 logi [1:3] TRUE FALSE TRUE

~~~

But what about:


~~~sourcecode
str(cats$coat)
~~~



~~~output
 Factor w/ 3 levels "black","calico",..: 2 1 3

~~~

Another important data structure is called a *factor*. Factors usually look like character data, but are typically used to represent categorical information. For example, let's make a vector of strings labeling cat colorations for all the cats in our study:


~~~sourcecode
coats <- c('tabby', 'tortoiseshell', 'tortoiseshell', 'black', 'tabby')
coats
~~~



~~~output
[1] "tabby"         "tortoiseshell" "tortoiseshell" "black"        
[5] "tabby"        

~~~



~~~sourcecode
str(coats)
~~~



~~~output
 chr [1:5] "tabby" "tortoiseshell" "tortoiseshell" "black" ...

~~~

We can turn a vector into a factor like so:


~~~sourcecode
CATegories <- factor(coats)
str(CATegories)
~~~



~~~output
 Factor w/ 3 levels "black","tabby",..: 2 3 3 1 2

~~~

Now R has noticed that there are three possible categories in our data - but it also did something surprising; instead of printing out the strings we gave it, we got a bunch of numbers instead. R has replaced our human-readable categories with numbered indices under the hood:


~~~sourcecode
typeof(coats[1])
~~~



~~~output
[1] "character"

~~~



~~~sourcecode
typeof(CATegories[1])
~~~



~~~output
[1] "integer"

~~~

<!--sec data-title="Challenge 2" data-id="ch2" data-show=true data-collapse=false ces-->

Is there a factor in our `cats` data.frame? what is its name? Try using `?read.csv` to figure out how to keep text columns as character vectors instead of factors; then write a command or two to show that the factor in `cats` is actually is a character vector when loaded in this way.

<!--endsec-->

In modeling functions, it's important to know what the baseline levels are. This is assumed to be the
first factor, but by default factors are labeled in alphabetical order. You can change this by specifying the levels:


~~~sourcecode
mydata <- c("case", "control", "control", "case")
factor_ordering_example <- factor(mydata, levels = c("control", "case"))
str(factor_ordering_example)
~~~



~~~output
 Factor w/ 2 levels "control","case": 2 1 1 2

~~~

In this case, we've explicitly told R that "control" should represented by 1, and
"case" by 2. This designation can be very important for interpreting the
results of statistical models!

<br>

---

## Lists

Another data structure you'll want in your bag of tricks is the `list`. A list is simpler in some ways than the other types, because you can put anything you want in it:


~~~sourcecode
list_example <- list(1, "a", TRUE, 1+4i)
list_example
~~~



~~~output
[[1]]
[1] 1

[[2]]
[1] "a"

[[3]]
[1] TRUE

[[4]]
[1] 1+4i

~~~



~~~sourcecode
list_example[2]
~~~



~~~output
[[1]]
[1] "a"

~~~



~~~sourcecode
another_list <- list(title = "Research Bazaar", numbers = 1:10, data = TRUE )
another_list
~~~



~~~output
$title
[1] "Research Bazaar"

$numbers
 [1]  1  2  3  4  5  6  7  8  9 10

$data
[1] TRUE

~~~

We can now understand something a bit surprising in our data.frame; what happens if we run:


~~~sourcecode
typeof(cats)
~~~



~~~output
[1] "list"

~~~

We see that data.frames look like lists 'under the hood' - this is because a data.frame is really a list of vectors and factors, as they have to be - in order to hold those columns that are a mix of vectors and factors, the data.frame needs something a bit more flexible than a vector to put all the columns together into a familiar table.

<br>

---

## Matrices

Last but not least is the matrix. We can declare a matrix full of zeros:


~~~sourcecode
matrix_example <- matrix(0, ncol=6, nrow=3)
matrix_example
~~~



~~~output
     [,1] [,2] [,3] [,4] [,5] [,6]
[1,]    0    0    0    0    0    0
[2,]    0    0    0    0    0    0
[3,]    0    0    0    0    0    0

~~~

and we can ask for and put values in the elements of our matrix with a couple of different notations:


~~~sourcecode
matrix_example[1,1] <- 1
matrix_example
~~~



~~~output
     [,1] [,2] [,3] [,4] [,5] [,6]
[1,]    1    0    0    0    0    0
[2,]    0    0    0    0    0    0
[3,]    0    0    0    0    0    0

~~~



~~~sourcecode
matrix_example[1][1]
~~~



~~~output
[1] 1

~~~



~~~sourcecode
matrix_example[1][1] <- 2
matrix_example[1,1]
~~~



~~~output
[1] 2

~~~



~~~sourcecode
matrix_example
~~~



~~~output
     [,1] [,2] [,3] [,4] [,5] [,6]
[1,]    2    0    0    0    0    0
[2,]    0    0    0    0    0    0
[3,]    0    0    0    0    0    0

~~~

<!--sec data-title="Challenge 3" data-id="ch3" data-show=true data-collapse=false ces-->

What do you think will be the result of `length(matrix_example)`? Try it. Were you right? Why / why not?

<!--endsec-->

<!--sec data-title="Challenge 4" data-id="ch4" data-show=true data-collapse=false ces-->

Make another matrix, this time containing the numbers 1:50, with 5 columns and 10 rows. Did the `matrix` function fill your matrix by column, or by row, as its default behaviour? See if you can figure out how to change this. (hint: read the documentation for `matrix`!)

<!--endsec-->

<!--sec data-title="Challenge 5" data-id="ch5" data-show=true data-collapse=false ces-->

Create a list of length two containing a character vector for each of the sections in this part of the workshop:

- Data types
- Data structures

Populate each character vector with the names of the data types and data structures we've seen so far.

<!--endsec-->

<!--sec data-title="Challenge 6" data-id="ch6" data-show=true data-collapse=false ces-->

Consider the R output of the matrix below:

~~~output
     [,1] [,2]
[1,]    4    1
[2,]    9    5
[3,]   10    7

~~~
What was the correct command used to write this matrix? Examine each command and try to figure out the correct one before typing them. Think about what matrices the other commands will produce.

1. `matrix(c(4, 1, 9, 5, 10, 7), nrow = 3)`
2. `matrix(c(4, 9, 10, 1, 5, 7), ncol = 2, byrow = TRUE)`
3. `matrix(c(4, 9, 10, 1, 5, 7), nrow = 2)`
4. `matrix(c(4, 1, 9, 5, 10, 7), ncol = 2, byrow = TRUE)`

<!--endsec-->



<br>

---

## Challenge solutions

<!--sec data-title="Solution to Discussion 1" data-id="disc1sol" data-show=true data-collapse=true ces-->

By keeping everything in a column the same, we allow ourselves to make simple assumptions about our data; if you can interpret one entry in the column as a number, then you can interpret *all* of them as numbers, so we don't have to check every time. This consistency, like consistently using the same separator in our data files, is what people mean when they talk about *clean data*; in the long run, strict consistency goes a long way to making our lives easier in R.

<!--endsec-->

<!--sec data-title="Solution to Challenge 1" data-id="ch1sol" data-show=true data-collapse=true ces-->


~~~sourcecode
x <- c(11:20)
subset <- x[3:5]
names(subset) <- c('S', 'W', 'C')
~~~

<!--endsec-->

<!--sec data-title="Solution to Challenge 2" data-id="ch2sol" data-show=true data-collapse=true ces-->


~~~sourcecode
cats <- read.csv(file="../data/feline-data.csv", stringsAsFactors=FALSE)
str(cats$coat)
~~~



~~~output
 chr [1:4] "calico" "black" "tabby" "tabby"

~~~
Note: new students find the help files difficult to understand; make sure to let them know that this is typical, and encourage them to take their best guess based on semantic meaning, even if they aren't sure.

<!--endsec-->

<!--sec data-title="Solution to Challenge 3" data-id="ch3sol" data-show=true data-collapse=true ces-->

What do you think will be the result of `length(x)`?


~~~sourcecode
length(matrix_example)
~~~



~~~output
[1] 18

~~~

Because a matrix is really just a vector with added dimension attributes, `length` gives you the total number of elements in the matrix.

<!--endsec-->

<!--sec data-title="Solution to Challenge 4" data-id="ch4sol" data-show=true data-collapse=true ces-->

Make another matrix, this time containing the numbers 1:50, with 5 columns and 10 rows. Did the `matrix` function fill your matrix by column, or by row, as its default behaviour? See if you can figure out how to change this. (hint: read the documentation for `matrix`!)


~~~sourcecode
> x <- matrix(1:50, ncol=5, nrow=10)
> x <- matrix(1:50, ncol=5, nrow=10, byrow = TRUE) # to fill by row
~~~

<!--endsec-->

<!--sec data-title="Solution to Challenge 5" data-id="ch5sol" data-show=true data-collapse=true ces-->


~~~sourcecode
dataTypes <- c('double', 'complex', 'integer', 'character', 'logical')
dataStructures <- c('data.frame', 'vector', 'factor', 'list', 'matrix')
answer <- list(dataTypes, dataStructures)
~~~
Note: it's nice to make a list in big writing on the board or taped to the wall listing all of these types and structures - leave it up for the rest of the workshop to remind people of the importance of these basics.

<!--endsec-->


<!--sec data-title="Solution to Challenge 6" data-id="ch6sol" data-show=true data-collapse=true ces-->

Consider the R output of the matrix below:

~~~output
     [,1] [,2]
[1,]    4    1
[2,]    9    5
[3,]   10    7

~~~
What was the correct command used to write this matrix? Examine each command and try to figure out the correct one before typing them. Think about what matrices the other commands will produce.

~~~sourcecode
> matrix(c(4, 1, 9, 5, 10, 7), ncol = 2, byrow = TRUE)
~~~

<!--endsec-->
