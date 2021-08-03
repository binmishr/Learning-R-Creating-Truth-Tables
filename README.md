# Learning-R-Creating-Truth-Tables

A short one for today: in this post we will learn how to easily create truth tables with R and will contribute our code to the growing repository of Rosetta code. I hope that you will learn a few tricks along the way, so read on!

We have covered bits of code that I contributed to Rosetta Code on this blog before (see Category: Rosetta Code). This time we want to solve the following task:

    Truth table
    A truth table is a display of the inputs to, and the output of a Boolean function organized as a table where each row gives one combination of input values and the corresponding value of the function.

    Task

        Input a Boolean function from the user as a string then calculate and print a formatted truth table for the given function.
        (One can assume that the user input is correct).
        Print and show output for Boolean functions of two and three input variables, but any program should not be limited to that many variables in the function.
        Either reverse-polish or infix notation expressions are allowed.

The core of a truth table is a permutation of all TRUE and FALSE statements for all variables (= letters), which we extract from a Boolean function x. Fortunately, we created such a permutation function a few posts ago (see Learning R: Permutations and Combinations with Base R), so that we can adapt it accordingly: expand.grid(rep(list(c(FALSE, TRUE)), length(vars))). We then add another column with the resulting evaluation of the Boolean function and return the resulting table:

truth_table <- function(x) {
  vars <- unique(unlist(strsplit(x, "[^a-zA-Z]+")))
  vars <- vars[vars != ""]
  perm <- expand.grid(rep(list(c(FALSE, TRUE)), length(vars)))
  names(perm) <- vars
  perm[ , x] <- with(perm, eval(parse(text = x)))
  perm
}

Now, let us try some examples:

"%^%" <- xor # define unary xor operator

truth_table("!A") # not
##       A    !A
## 1 FALSE  TRUE
## 2  TRUE FALSE

truth_table("A | B") # or
##       A     B A | B
## 1 FALSE FALSE FALSE
## 2  TRUE FALSE  TRUE
## 3 FALSE  TRUE  TRUE
## 4  TRUE  TRUE  TRUE

truth_table("A & B") # and
##       A     B A & B
## 1 FALSE FALSE FALSE
## 2  TRUE FALSE FALSE
## 3 FALSE  TRUE FALSE
## 4  TRUE  TRUE  TRUE

truth_table("A %^% B") # xor
##       A     B A %^% B
## 1 FALSE FALSE   FALSE
## 2  TRUE FALSE    TRUE
## 3 FALSE  TRUE    TRUE
## 4  TRUE  TRUE   FALSE

truth_table("S | (T %^% U)") # 3 variables with brackets
##       S     T     U S | (T %^% U)
## 1 FALSE FALSE FALSE         FALSE
## 2  TRUE FALSE FALSE          TRUE
## 3 FALSE  TRUE FALSE          TRUE
## 4  TRUE  TRUE FALSE          TRUE
## 5 FALSE FALSE  TRUE          TRUE
## 6  TRUE FALSE  TRUE          TRUE
## 7 FALSE  TRUE  TRUE         FALSE
## 8  TRUE  TRUE  TRUE          TRUE

truth_table("A %^% (B %^% (C %^% D))") # 4 variables with nested brackets
##        A     B     C     D A %^% (B %^% (C %^% D))
## 1  FALSE FALSE FALSE FALSE                   FALSE
## 2   TRUE FALSE FALSE FALSE                    TRUE
## 3  FALSE  TRUE FALSE FALSE                    TRUE
## 4   TRUE  TRUE FALSE FALSE                   FALSE
## 5  FALSE FALSE  TRUE FALSE                    TRUE
## 6   TRUE FALSE  TRUE FALSE                   FALSE
## 7  FALSE  TRUE  TRUE FALSE                   FALSE
## 8   TRUE  TRUE  TRUE FALSE                    TRUE
## 9  FALSE FALSE FALSE  TRUE                    TRUE
## 10  TRUE FALSE FALSE  TRUE                   FALSE
## 11 FALSE  TRUE FALSE  TRUE                   FALSE
## 12  TRUE  TRUE FALSE  TRUE                    TRUE
## 13 FALSE FALSE  TRUE  TRUE                   FALSE
## 14  TRUE FALSE  TRUE  TRUE                    TRUE
## 15 FALSE  TRUE  TRUE  TRUE                    TRUE
## 16  TRUE  TRUE  TRUE  TRUE                   FALSE
