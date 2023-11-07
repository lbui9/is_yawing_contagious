# Assignment 6: Who busts the Mythbusters?


**Summary:** An assignment for exploring data distributions, and practicing how to conduct hypothesis tests using the infer package.

- [Assignment 6: Who busts the Mythbusters?](#assignment-6-who-busts-the-mythbusters)
  - [Instructions](#instructions)
  - [About the dataset](#about-the-dataset)
  - [Exercises](#exercises)
  - [How to submit](#how-to-submit)
  - [Cheatsheets](#cheatsheets)
  - [Appendix](#appendix)

## Instructions

Obtain the GitHub repository you will use to complete the assignment, which contains a starter file named `mythbusters.Rmd`.
This assignment will guide you through examples demonstrating how to conduct hypothesis tests and calculate confidence intervals using the `infer` package.
Read the [About the dataset](#about-the-dataset) section to get some background information on the dataset that you'll be working with.
Each of the below [exercises](#exercises) are to be completed in the provided spaces within your starter file `mythbusters.Rmd`.
Then, when you're ready to submit, follow the directions in the **[How to submit](#how-to-submit)** section below.

## About the dataset

The dataset we are using in this assignment comes from an experiment shown on an episode of the *Mythbusters* television show (Season 3, Episode 28, *Is Yawning Contagious?*, first aired on March 9, 2005), a science entertainment TV program that aired on the Discovery Channel.
The show's build team, Kari Byron, Scottie Chapman, and Tory Belleci, conducted the experiment in order to test the following hypothesis,

> It becomes more likely for a person to yawn if another person near them yawns.

To conduct the experiment, the team drove 50 volunteers, one at a time, to a flea market in a van, and one of the team members, would either yawn (treatment) or not yawn (no treatment) during the ride.
It was randomly determined ahead of time whether or not Kari yawned.
Therefore, the 50 people were split into two randomly assigned groups:

*   **Treatment group:** 34 people where a person near them yawned

*   **Control group:** 16 people where there wasn't a person yawning near them

The results of the experiment are presented in the following table,

group     | yawns | total | fraction_yawned
:--------:|:-----:|:-----:|:---------------:
Control   | 4     | 16    | 0.2500
Treatment | 10    | 34    | 0.2941

They concluded that the myth was confirmed because there was a 4% increase in yawns between the control and treatment groups.
However, their analysis did not appear to include a formal hypothesis test, nor did the team discuss how likely it was that random chance alone could generate their results.
Without this kind of analysis, we can not meaningfully determine whether the data supports the conclusion that yawning is contagious.

Our main activity in this assignment will be fact-checking the Mythbusters by carrying out a formal hypothesis test using the `infer` package.
This will allow us to assess how likely it is that a random chance model could generate the observed experimental results and whether or not the collected data allow us to infer the same conclusion as the *Mythbusters* build team.

The dataset `experimental_data` contains 3 variables and 50 rows,

| Variable                 | Description                                                                          |
| --------                 | ------------------------------------------------------------------------------------ |
| `subject_id` | Integer uniquely identifying an experiment volunteer                                 |
| `yawn`       | Whether or not a volunteer yawned, *yes* if he/she did, *no* if he/she did not       |
| `group`      | Specifies if the volunteer was part of the *Control* group or the *Treatment* group  |


## Exercises


1. 
    Whenever we conduct an experiment, we need to know what variable we will change between the treatment and control groups, and what the kinds of outcomes we will be measuring.
    
    Look again at the three variables for this experiment in the `experimental_data` dataset.
    Which one is the **explanatory** variable and which one is the **response** variable?
    What value in the **response** variable should be classified as a success?

2. 
    To conduct the hypothesis test, we need to simulate the null distribution.

    The null distribution represents the range of outcomes for this experiment if yawning has no effect on the treatment group and is due to random chance alone.
    To generate a null distribution we need to know how the observed experimental result was calculated, as we will need to repeat this same calculation during our simulations.

    Review the results table in the [About the dataset](#about-the-dataset) section again.
    What quantity should we use or compute in order to build the null distribution?
    
    > In other words, what variable will lie along the x-axis of the probability density function of our null distribution (i.e. similar to the graph on slide 8 of video 8 for this module, "Visualizing the Null Distribution").

    Choose the correct answer from the options below.
    
    1.  *The average number of `yawns` in the treatment group*
    
    2.  *The average value of `fraction_yawned` in the treatment group*
    
    3.  *The average difference in `yawns` between the treatment and control groups*
    
    4.  *The average difference in `fraction_yawned` between the treatment and control groups*

3. 
    Running a hypothesis test for this dataset requires us to fill in the following code template that contains four functions from the `infer` package,
    
    ```r
    yawn_null <- experimental_data %>%
      specify(<variable1> ~ <variable2>, success = "...") %>%
      hypothesize(null = "...") %>%
      generate(reps = ...,  type = "...") %>%
      calculate(stat = "...",  order = c("...", "..."))
    ```
    
    We will step through filling in this template over the next few questions.
    
    We start with the function `specify()`, which we see has the following structure,
    
    ```r
    specify(<variable1> ~ <variable2>, success = "...")
    ```
    
    `<variable1>`, `<variable2>`, and `"..."` are placeholders.
    Using your answers from Exercise 1 (and the *Hint* below), figure out what to fill in for these three placeholders in the answer file.
    **You only need to write out the `specify()` function for this exercise.**
    
    > #### Hint
    >
    > The relationship between the response and explanatory variables is written using a special formula syntax, `<variable1>` ~ `<variable2>`.
    The term on the left side of the tilde, `<variable1>`, is the *response variable*.
    The term on the right side of the tilde, `<variable2>`, is the *explanatory variable*.
    >
    > In other words, the formula should look like this:
    >
    > `response_variable ~ explanatory_variable`

4. 
    The next step in our code template for generating a null hypothesis is the `hypothesize()` function,
    
    ```r
    hypothesize(null = "...")
    ```
    
    This function only takes one input, and you have two choices for what to use (see below). What input should we use? Replace the placeholder in the answer file with its correct value.
    
    > #### Values for `null`
    >
    > *  `"independence"`: used when the explanatory and response variables both refer to columns in the dataset.
    >
    > *  `"point"`: used when we want to compare a column against a reported point estimate (i.e. a single number such as the mean or median).


5. 
    The third step in our code template is the `generate()` function,
    
    ```r
    generate(reps = ...,  type = "...")
    ```
    
    We use the `reps` input to set how many simulation trials we want to run to generate the null distribution.
    This input should be set equal to an integer value.
    The more trials we run, the more accurate our simulated range of outcomes will be, although it will also mean it will take our code longer to run.
    
    The `type` keyword is used to choose the type of simulation that we want to run. Generating a null distribution for this dataset means we want to see what our outcomes will look like if the response variable and explanatory variable have no relationship to each other.
    
    If we want to run *10000* (ten thousand) simulations to create the *null* distribution for this dataset, what should I replace the placeholders with? Replace the placeholders in the answer file with their correct values. (Hint: what values did they use in the lecture videos?)

    > #### Values for `type`
    >
    > There are three choices for `type`,
    >
    > *   `"permute"`: Run the simulation by randomly shuffling the order of the cells in the explanatory and response variables.
        This random shuffling procedure is done separately in each column, so the relationship between different columns in each row is not preserved. Typically used for creating null distributions by disassociating the relationships in the original dataset.
    > 
    > *   `"bootstrap"`: Run the simulation by randomly sampling rows *with replacement* from the dataset.
        The relationship between different columns in each row is preserved.
        Typically used for calculating confidence intervals by resampling rows from the original dataset.
    >
    > *   `"simulate"`: Run the simulation by flipping a weighted coin, meaning that heads or tails may not be equally probable (i.e. the coin may be unfair).
        This coin, being virtual, may have more than two sides, meaning there are several discrete outcomes that each have certain probabilities of happening.
        This `type` is typically needed if you are using `null = "point"` in `hypothesize()` and your response variable is categorical.
    
    
6. 
    The last step in our code template is the `calculate()` function,
    
    ```r
    calculate(stat = "...",  order = c("...", "..."))
    ```
    
    The first argument, `stat`, is where we specify what exactly it is that we're calculating, the *test statistic* which must mirror how the observed experimental result was calculated.
    
    The second argument is `order`, which specifies the order of subtraction between the two groups in the **explanatory** variable. We need to supply a vector of 2 items: the two categories in the explanatory variable. The test statistic (which is either the *difference in means* or the *difference in proportions*) will be calculated as the mean/proportion of the first cateogy in this vector *minus* the mean/proportion of the second cateogy in the vector.
    
    We want to calculate our test statistics as `Treatment` - `Control`.
    
    Based on your response to question 2 and the above information, replace the placeholders with their correct values.

    > #### Values for `stat`
    >
    > Here are some of most useful arguments we can pass to the `stat` parameter. The full list is available in the [Appendix](#appendix) at the end of this file.
    >
    > *   `"diff in means"`: Calculate the mean of the numerical response variable for the two groups defined in the categorical explanatory variable.
        Subtract these two means. We typically use this if the response variable is continuous.
    >
    > *   `"diff in props"`: Calculate the number of successes in the *categorical* response variable and convert this to a fraction (proportion) in each of the two groups defined in the *categorical* explanatory variable.
        Subtract these two fractions. We typically use this option if the response variable is categorical.
    
    
7. 
    Take your answers to questions 3 through 6 and combine them to fill in the template,
    
    ```r
    yawn_null <- experimental_data %>%
      specify(formula = <variable1> ~ <variable2>, success = "...") %>%
      hypothesize(null = "...") %>%
      generate(reps = ...,  type = "...") %>%
      calculate(stat = "...",  order = c("...", "..."))
    ```
    
    This will simulate the null distribution for the `yawn` experiment.
    This may take a few seconds to run in RStudio.


8. Congratulations! You have just simulated the data for that make up the null distribution and saved it to the `yawn_null` variable. `yawn_null` should be a two column dataframe, containing 10,000 observations of a variable called `stat`. This variable is the difference in proportions in each of our permutations (random shuffles) of the original data. Before we move on to calculating p-values, let's take a few questions to examine the data that we just generated.
  
    i. Create a histogram of the data in `yawn_null`. Play around with the `bins` or `binwidth` parameter until you get a histogram without gaps between the bars.
  
    ii. Create a density plot of the data in `yawn_null` using the `geom_density` geom function (instead of `geom_histogram`).
    
    iii. Do you see how the density plot is jagged? We can remove this by changing how the density curve is smoothed. Create a new density plot using your code from part (ii) but add the the `adjust` parameter  to the `geom_density()` function as in this example:
    
        geom_density(mapping = aes(...), adjust = 5)
  
    iv. Where is the center of each distribution? Does this make sense given what you know about the data (i.e. would you expect the difference of proportions to be centered on this value)?

9. There are advantages and disadvantages to using either `geom_histogram()` or `geom_density()` to represent a data distribution, so it can be preferable to show both types on the same plot. Take the code you wrote in Exercise 8 (parts (i) and (iii)) and try doing this yourself. Remember, to overlay geoms you add them together like in the code template below (you will need to replace the ellipses: `...`),

       ```{r}
       ggplot(data = ...) +
         geom_histogram(mapping = aes(...), ...) +
         geom_density(mapping = aes(...))
       ```

    Does the plot look okay, or do you notice a problem?

10. 
    The problem you should have noticed in Exercise 9 is that the vertical axis scale for the histogram and density plots do not match. This is because the bar heights in the histogram *count* the number of data points that fall within a given range of values while the density curve's height is a fraction, and so they are not directly comparable.

    You can fix this discrepancy by *normalizing* the bar heights in the histogram by dividing the height of each bar by the number of data points within the distribution (in this case, 10,000).

    Luckily for us, there is a convenient way to do this using ggplot2: just add the input `y = ..density..` inside the `aes()` function of `geom_histogram()`. This converts the histogram into a **probability mass function (PMF)**.

    Try converting your histogram from Exercise 9 into a PMF. Copy and paste your code from Exercise 9 and then add `y = ..density..` inside the `aes()` function of `geom_histogram()`.

    > #### Pro Tip
    >
    > Using a PMF or density curve to represent your distribution allows you to directly compare groups of data containing different numbers of observations.

11. 
    Now that we have a simulated null distribution, and explored what the PMF of that distribution looks like, let's quantify how likely it is that a random chance model would generate the Mythbuster's experimental result.

    We will do this by computing the *p*-value of a **one-sided hypothesis test**, which is just the probability that the result of a simulation trial is the same or more extreme than the experimental result.
    We will also assume a significance level of &alpha; = 0.05.
    **If our *p*-value is less than &alpha; = 0.05**, then we will conclude that we have sufficient evidence to reject the null hypothesis in favor of the alternative hypothesis.
    
    First, let's use `infer` to compute the experimental result for comparison with the null distribution.
    Fill in the template below with the values you determined for `specify()` and `calculate()`,
    
    ```r
    yawn_obs_stat <- experimental_data %>%
      specify(formula = <variable1> ~ <variable2>, success = "...") %>%
      calculate(stat = "...",  order = c("...", "..."))
    ```
    
    Notice that we just took the template from Exercise 7 and removed the `hypothesize()` and `generate()` lines.
    
    Next, if we wanted to compute the *p*-value manually, we would need to count how many simulations gave us a result that was the same or more extreme than the experimental result and then divide by 10,000, which is the total number of simulations we ran.
    Lucky for us, the `infer` package has a convenience function, `get_p_value()`, for calculating p-values so that we don't have to do it manually.
    
    To compute the *p*-value of a one-sided hypothesis test, we run:
    
    ```r
    yawn_null %>%
      get_p_value(obs_stat = yawn_obs_stat, direction = "...")
    ```
    
    Because we care about scenarios in which more people yawned in the treatment group, we care about values of the test statistic that are greater than the observed value, i.e. they lie to the right hand side of the observed value in the graph of the null distribution. We therefore want to use `direction = "right"` when calculating the p-value.
    
    > #### Important
    >
    > Why `direction = "right"` versus `"left"`? Remember that a p-value is the probability of getting values as or more extreme to than the observed statistic.
    >
    > Whether you should use `direction = "right"` or `direction = "left"` depends on whether what order you supplied to the `c` function in Exercises 6 & 7 (and this Exercise), because this will determine which way is "more extreme". 
    
    To visualize the range of outcomes in the null distribution that are the same or more extreme than `yawn_obs_stat`, we run,
    
    ```r
    yawn_null %>%
      visualize() +
      shade_p_value(obs_stat = yawn_obs_stat, direction = "right")
    ```
    
    Based on these results and a significance level of &alpha; = 0.05, conclude whether or not we have sufficient evidence to reject the null hypothesis. Justify your answer.
    
    **Warning:** our null distribution is generated randomly, so you will get a different set of values in `yawn_null` and hence a slightly different `p-value` each time you run your code! To generate the same set of "random" numbers each time we run our code we need to set the starting *seed* for the random number generator:
    
    * You can do this by adding this line at the *start* of the code chunk for Exercise 7 (on a line immediately before the code that creates the `yawn_null` variable): `set.seed(42)`. 
    
    * You may change the number that you use as the seed if you wish - however, [the answer to life, the universe, and everything](https://www.google.com/search?q=the+answer+to+life+the+universe+and+everything&oq=the+answer+to+life+the+universe+and+everything) is a commonly chosen random seed...
    
    * Making this change to exercise 7 is optional, as the small variations in the *p-value* should not affect your overall conclusions about whether we can reject the null hypothesis.
    
    > #### Pro Tip
    >
    > If you want to use symbols like &alpha; in your RMarkdown file, you cannot enter the symbol directly. RMarkdown only accepts the standard Latin alphabet, Roman numerals, and the basic punctuation symbols that you have on your keyboard.
    >
    > To get special characters on your keyboard, you will need to use RMarkdown's equation formatting syntax using the `$` symbol. For example, `$\alpha$` will be converted into &alpha; and `$\alpha = 0.05$` will be converted into &alpha; = 0.05



## How to submit

To submit your assignment, follow the two steps below.
Your homework will be graded for credit **after** you've completed both steps!

1.  Save, commit, and push your completed R Markdown file so that everything is synchronized to GitHub.
    If you do this right, then you will be able to view your completed file on the GitHub website.

2.  Knit your R Markdown document to the PDF format, export (download) the PDF file from RStudio Server, and then upload it to *Assignment 6* posting on Blackboard.

## Cheatsheets

You are encouraged to review and keep the following cheatsheets handy while working on this assignment:

*   [dplyr cheatsheet][dplyr-cheatsheet]

*   [ggplot2 cheatsheet][ggplot2-cheatsheet]

*   [RStudio cheatsheet][rstudio-cheatsheet]

*   [R Markdown cheatsheet][rmarkdown-cheatsheet]

*   [R Markdown reference][rmarkdown-reference]


## Appendix

> The available choices for `stat` paramter are as follows:
>
> *   `"mean"`: For simulations based on a single numerical variable.
>    Compute the mean of each simulation trial.
>
> *   `"median"`: For simulations based on a single numerical variable.
    Find the median of each simulation trial.
>
> *   `"sum"`: For simulations based on a single numerical variable.
    Compute the sum of each simulation trial.
>
> *   `"sd"`: For simulations based on a single numerical variable.
    Compute the standard deviation of each simulation trial.
>
> *   `"prop"`: For simulations based on a single categorical variable.
    Count the number of successes and convert this to a fraction (proportion).
>
> *   `"count"`: For simulations based on a single categorical variable.
    Count the number of successes.
>
> *   `"diff in means"`: Calculate the mean of the numerical response variable for the two groups defined in the categorical explanatory variable.
    Subtract these two means.
>
> *   `"diff in medians"`: Find the median of the numerical response variable for the two groups defined in the categorical explanatory variable.
    Subtract these two medians.
>
> *   `"diff in props"`: Calculate the number of successes in the *categorical* response variable and convert this to a fraction (proportion) in each of the two groups defined in the *categorical* explanatory variable.
    Subtract these two fractions.
>
> *   `"Chisq"`: Calculate the chi-squared statistic for a response and explanatory variable that are both categorical and contain two or more groups each.
>
> *   `"F"`: Calculate the *F* statistic for a numerical response variable and categorical explanatory variable with two or more groups.
>
> *   `"t"`: Calculate the two-sample t-test statistic for a numerical response variable and categorical explanatory variable with two groups.
    This is similar to the `"diff in means"` calculation.
>
> *   `"z"`: Calculate the two-sample z statistic for a numerical response variable and categorical explanatory variable with two groups.
    It is assumed that the number of samples in your dataset is large, otherwise the `"t"` test is preferred.
    This is similar to the `"diff in means"` calculation.
>
> *   `"slope"`: Calculate the slope using linear regression for a response and explanatory variable that are both numerical.
>
> *   `"correlation"`: Calculate the correlation coefficient for a response and explanatory variable that are both numerical.



[dplyr-cheatsheet]:     https://github.com/rstudio/cheatsheets/raw/master/data-transformation.pdf
[ggplot2-cheatsheet]:   https://github.com/rstudio/cheatsheets/raw/master/data-visualization-2.1.pdf
[rstudio-cheatsheet]:   https://github.com/rstudio/cheatsheets/raw/master/rstudio-ide.pdf
[rmarkdown-reference]:  https://www.rstudio.com/wp-content/uploads/2015/03/rmarkdown-reference.pdf
[rmarkdown-cheatsheet]: https://github.com/rstudio/cheatsheets/raw/master/rmarkdown-2.0.pdf
