# Assignment 8: Agent-Based Models

In this assignment, we will revisit the SIR (Susceptible-Infectious-Recovered) model of infectious disease spread.

When we previously studied this, we built a *system dynamics* model to simulate the spread of the diseases. The had three "buckets" (one for each category of person), and equations that described the flow of people between those buckets.

There is an alternative way that we can simulate the spread of the disease: an **Agent-Based Model**.

In an agent-based model (ABM), we simulate every person (or *agent*). Instead of categories of identical people, we create a computer program that keeps track of each person individually.

This allows us to investigate much more complex interactions. It also allows us to have agents with different characteristics and behaviors, rather than assuming that every person is the same.

## About the model

We have already written a function that will run an agent-based simulation of the SIR model. This function is called `run_abm()`, and you can find the code for it in the set-up chunk.

> #### Optional: How does the ABM code work?
>
> You are not required to read the code for the `run_abm()` function, although you are welcome to do so if you want to understand how it works. Essentially:
>
> * We create a tibble called `population` whose rows represent the agents (i.e. people) in the ABM.
> * We use a *for*-loop to iterate over the time steps of the model. (You can think of each time step as a day)
> * At each time step, the agents will randomly interact with a small number of other agents. If an agent bumps into another agent who is infectious, then there is a probability that they will become infected.
> * We keep track of how many susceptible, infectious, and recovered agents were present at each time step, and create a new tibble of these values at the end of the simulation. This tibble is then returned by the `run_abm()` function.

The output of the `run_abm()` function will be a tibble that looks something like this:

| time | S | I | R |
|------|---|---|---|
| 1    | 99 | 1 | 0 |
| 2    | 98 | 2 | 0 |
| 3    | 96 | 4 | 0 |
| etc. | ... | ... | ... |

Each row in this tibble represents the results at the end of a time step (you can treat a time step to be roughly equivalent to a day). The `time` column records the number of the time step. The `S`, `I`, and `R` columns hold the counts of susceptible, infectious, and recovered people respectively.

You can customize the behavior of the `run_abm()` function by changing the input arguments. The following table describes these inputs and the effect they have:

| Parameter | Default | Description |
| --------- | ------- | ----------- |
| `population_size` | 100 | The number of agents in the simulation |
| `initial_infected` | 1 | The number of people infected when the simulation begins |
| `time_steps` | 50 | The number of time steps to run the model for |
| `base_prob_infection` | 0.05 | The probability of infection when a susceptible agent interacts with an infected agent |
| `time_to_recover` | 10 | The number of time steps it takes for an infected agent to recover after infection |
| `min_interactions` | 1 | The *minimum* number of other agents that each agent interacts with per time step. |
| `max_interactions` | 10 | The *maximum* number of other agents that each agent interacts with per time step. |
| `random` | FALSE | If `TRUE`, the `run_abm()` function will use `set.seed()` to generate repeatable results. |
| `masking` | FALSE | Whether some agents should use masks. Set to `TRUE` to use enable. |
| `masking_prob` | NA | The probability (between 0 and 1) that an agent uses a mask. |
| `mask_effectiveness` | NA | The effectiveness (between 0 and 1) of a mask. The probability of infection in an interaction will be reduced by this fraction. |
| `isolation` | FALSE | Determines whether agents will "self-isolate", i.e. stop interacting with other agents after detecting that they are infected. `FALSE` by default, which means that infected agents will interact normally throughout their infection. |
| `time_to_isolation` | 3 | Time steps before an infected agent stops interacting with other agents. |




## Exercises

1. Copy and paste the following line of code into a new code chunk, and run it. It will take some time to run, so be patient.

   ````
   ```{r, results="hide"}
   results_ex1 <- run_abm()
   ```
   ````

   This code will run an agent-based model simulation as described in the *About the Model* section of these instructions and save the resulting dataframe in a new R variable called `results_ex1`.

   We need to use the `results = "hide"` option for the code chunk to hide the progress bar in the PDF. You will also need to use this option in any subsequent code chunks with the `run_abm()` function, but do not use it in code chunks that produce output we want to see, such as graphs.
   
   If the function takes more than a minute or two to finish, then you may want to read this box on how to store the results so that you do not have to repeatedly re-run the code. Otherwise, you can skip this box and continue with the rest of the exercise.
   
   > #### But it's so slow...
   >
   > One of the downsides of agent-based models is that they can take a long time to run.
   >
   > We are going to call the `run_abm()` function 5 separate times in this assignment. If it takes 12 minutes to run each time, then it will take over an hour to knit to a PDF!
   >
   > One way that we can make this a bit faster is to save (or "cache") the results of the `run_abm()` function each time. We can do this by saving the results of the `run_abm()` function to a file (make sure you use a different file name each time). We will also use an *if-else* statement to check if the file already exists. If it does, we will load the results from the file instead of re-running the function.
   >
   > Here is an example for this first exercise, which saves to (and loads from) a *.rds* file, which is a type of file that R can use to store dataframes:
   >
   > ```r
   > if (file.exists("results_ex1.rds")) {
   >   results_ex1 <- readRDS("results_ex1.rds")
   > } else {
   >   results_ex1 <- run_abm()
   >   saveRDS(results_ex1, "results_ex1.rds")
   > }
   > ```
   > 
   > You should see that the first time you run this code, it creates a file called `results_ex1.rds` in your assignment's project folder (i.e. look in the *Files* tab in the bottom right pane of RStudio).
   >
   > If you realize that you have made a mistake and need to re-run a particular call to the `run_abm()` function *after you have already cached the results in a file*, then you will need to delete the corresponding file! (I.e. for the example above, we would need to delete the `results_ex1.rds` file from our assignment's folder.)
   >
   > Be very careful with caching! It is easy to cache some results, and then later change some code and not realize that you are still getting the cached results from before instead of new results.
    
   When the code has finished running, inspect the `results_ex1` dataframe and answer the following questions:
    
   1. What are the columns called that hold the counts of susceptible, infectious, and recovered people?
    
   2. How many agents (i.e. people) were in this simulation in total?
    
   3. How many time steps did the simulation run for?

   Commit your work after finishing this exercise.

3. Create a line graph showing the number of susceptible, infectious, and recovered people versus time in the simulation dataframe from Exercise 1. This graph should contain three different colored lines (one for each of the three states), as well as a legend that indicates which of the lines corresponds to each state. Time should be on the x-axis, and the counts of people should be on the y-axis. (As with all your graphs, don't forget to add a title and appropriate axis labels.)

   Write a description (about a paragraph) of the trends that you see in this graph.

   Commit your work after finishing this exercise.

4. In the rest of this assignment, we will be repeatedly re-running the agent-based model (with different parameters) and plotting more graphs like the one in 2. So that we don't need to repeatedly copy and paste our graph code, let's instead encapsulate the graph code within a function. Then, whenever we want to create another graph of the SIR curves, we can just call this function.

   Create a function called `plot_sir` that takes two arguments as input: (1) the dataframe of results created by the `run_abm()` function, and (2) a character string to be the title of the graph. This function should create the same graph graph that you created in Exercise 2.
    
   You may wish to refer back to the first interactive tutorial (or the [corresponding section of the textbook](https://cdsbook.github.io/book/src/book/03_r_programming_chapter.html#functions)) to refresh your memory on how to create functions.
    
   To get you started, here is an example code template:
    
   ```{r}
   plot_sir <- function(.data, title){
       .data %>%
         ggplot() +
           ... +
           labs(..., title = title)
   }
   ```
   
   Hint: you should be able to use your code from Exercise 2 inside this function with minimal modification.
    
   Once you have created the function, add more code to your answer Rmd file to run this function on the `results_ex1` dataframe from Exercise 1. (The graph that it displays should look the same as the one from Exercise 2).

   Commit your work after finishing this exercise.

5. Run the agent-based simulation again (by putting the `run_abm()` function in a new code chunk) but add an argument to the function to change the *probability of infection* to 0.1. (Hint: refer back to the *About the Model* section of these instructions.)

   Assign the resulting dataframe to a new variable called `results_ex4`. 
   
   > As in exercise 1, if the function takes a long time to run then you may wish to cache your results to avoid having to wait for the function to run again (*make sure you change the name of the file you cache the results in!*).
    
   Then use your `plot_sir()` function from Exercise 3 to create a plot of this new SIR model.

   Commit your work after finishing this exercise.

6. 
    1. Create a line graph (just one graph) with two lines: one showing the number of infectious people from the first simulation, and a second line showing the number of infectious people in the second simulation. These counts should be on the y-axis, and time should again be on the x-axis. The two lines should be different colors, and there should again be a legend so that allows us to distinguish which line came from which simulation.
    
       Hint: remember that these two columns exist in different dataframes...
        
       Write a pargraph describing the differences that you see between the two infection curves in this graph. Discuss how any differences that you see might be related to the changes in the input parameters of the model.
    
    2. Create another function called `plot_comparison()` that creates the same graph from part (i) of this exercise (because this is another graph that we will want to reproduce in future exercises).
    
       This graph will need to accept two dataframes as its first two arguments, and then the title of the graph as a third argument.
        
       For example, here is a code template to get you started:
        
       ```r
       plot_comparison <- function(df1, df2, title){
         ...
       }
       ```
        
       As with the last function, you should be able to put your graph code from part (i) inside this function with minor modifications.
    
       After your function code, add another code chunk that runs your new `plot_comparison()` function to reproduce the same graph as you created in part (i).

   Commit your work after finishing this exercise.

7. Re-run the agent-based simulation, but this time add a parameter to change the minimum number of contacts to 5. (This should be the only argument you pass to the `run_abm()` function, i.e. omit the parameter you added in 4.)

   Assign the resulting dataframe to a new variable (e.g. `results_ex6`. results_6).
    
   Run the `plot_sir()` function to create a graph of the new SIR model, and also run the `plot_comparison()` function to compare the number of infections in this new model vs. the number of infections in the original simulation from Exercise 1.
    
   Write a desciption of any patterns and differences that you see in these graphs, along with a discussion of why they might exist.

   Commit your work after finishing this exercise.

8. Re-run the agent-based simulation, but this time change the following three parameters of the `run_abm()` function (and only these three):

   * Change the parameter that will set some of the agents to wear masks.
    
   * Set the probability of masking to `0.5` (which will mean that half of the simulation's agents will wear masks).
    
   * Set the effectiveness of masks to `0.3` (which will reduce the transmissibility of the disease between masked agents by 30%).
    
   Run the `plot_sir()` function to create a graph of the new SIR model, and also run the `plot_comparison()` function to compare the number of infections in this new model vs. the number of infections in the original simulation from Exercise 1.
    
   Write a desciption of any patterns and differences that you see in these graphs, along with a discussion of why they might exist.

   Commit your work after finishing this exercise.


9. Re-run the agent-based simulation, but this time change the following parameter *only* of the `run_abm()` function:

   * Set the parameter that allows agents to self-isolate. (This is meant to replicate the fact that once a person has become symptomatic they are aware that they have the disease and so avoid contacting other people. However, there is a period between contracting the disease and developing symptoms, during which an agent can contact other agents and spread the disease. In our simulation this period lasts 3 days by default.)
    
   Run the `plot_sir()` function to create a graph of the new SIR model, and also run the `plot_comparison()` function to compare the number of infections in this new model vs. the number of infections in the original simulation from Exercise 1.
    
   Write a desciption of any patterns and differences that you see in these graphs, along with a discussion of why they might exist.

   Commit your work after finishing this exercise.

10. Write a paragraph or two comparing the results of your different agent-based simulations. What do they imply for the actions a society might take to combat a disease?

    Write another paragraph describing some future direction that you could take this agent-based model to build on the analyses we have already done. (Feel free to use one of these prompts to get started: What other things that affect the spread of disease could we incorporate into our model? How might we check that our model's predictions actually match reality (e.g. how did we do that with the previous assignment's SIR model)?)

    Commit your work after finishing this exercise.



## Complete the Academic Integrity statement

Complete the academic integrity statement section, indicating whether you used an AI tool or not (in the same fashion that the Assignment 4 instructions taught you to do).

Don't forget that if you did use an AI tool, you need to submit the transcript of your conversation (either by creating a shareable link to the conversation, or by submitting an additional file to Blackboard that contains the full text of your conversation).

* How to create a shareable link to a ChatGPT conversation: [video tutorial](https://www.loom.com/share/7714b7c73d00411b89b5ec49f6393cb5?sid=28d4fc90-be7d-4170-8623-cc5261fed8f0)


## Submitting

To submit the main assignment (this is required whether or not you do the bonus exercise), follow the two steps below.

1.  Save, commit, and push your completed RMarkdown file so that everything is synchronized to GitHub.
    If you do this right, then you will be able to view your completed file on the GitHub website.

2.  Knit your RMarkdown document to PDF format, export (download) the PDF file from RStudio Server, and then upload it to Blackboard.

