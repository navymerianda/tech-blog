---
title: "World Of Data Science - Playing With Data!"
date: 2018-07-24
tags: ["data", "python", "R", "anaconda", "RStudio"]
draft: false
---

In case you're one of those who's looking to get into the world of data science. This article will help you get started with the basics for it.

Various stages are involved in the process of making data meaningful, so let's look at `pre-processing data` for now. Before we start with that, let's make sure you have the setup in place for it.

Please make sure to download `RStudio` for programming in R or `Anaconda` for python.

Depending on the OS, make sure to download the respective one from the link below:

[RStudio](https://www.rstudio.com/products/rstudio/download/)<br/>
[Anaconda](https://www.anaconda.com/download/#macos)

Once you have them both installed, let's jump into covering some simple scripts.

First, we look at preprocessing data in python:

Three key libraries that'll almost be required in every project that you'll take up later are:

> - numpy - Mathermatical calculations
> - matplotlib - data visualization
> - pandas - managing datasets

So, let's go ahead and fire up a sample script in Anaconda in the following way:

> - Open Anaconda application and choose the spyder option from the main menu. To the left of the screen, you'll have your editor where you'll insert the below code and save the new file with .py extension. And to the right of your screen, you'll see a variable and file explorer. On a windows system, select a particular line and `hit F9` to execute that specific line and on a mac system, choose a line and hit `(ctrl + command + enter).`

```
import numpy as np
import matplotlib.pyplot as pt
import pandas as ps
```

Once we have imported the libraries,  we'll move onto reading the data from a csv file. Please download the csv data file from the below link:

[Data.csv](http://www.mediafire.com/file/ba3pfrg458r1drh/Data.csv)

```
dataset = ps.read_csv('Data.csv');
```

After you have downloaded the csv to the working directory, run use this line of code to read the csv data.

You'll now notice a variable called dataset in your variable explorer on the right panel of your screen. Go ahead and click that variable to see the data in tabular format.

In case, `variable explorer` isn't displayed on the right panel, make sure to have it enabled from the menu bar anchored to the top of your screen: 

`View -> Panes -> Variable Explorer.`

This is how you load the dataset in Python, let's look at doing it in R.

Make sure to open up RStudio and create a new file with .R extension. You can have both python file and R file in the same folder as the data.csv.

```
dataset = read.csv('Data.csv')
```

In R, you will not have worry about `importing any libraries` for now, you can go ahead and directly run this code. In order to run the code on a mac, use `(command + enter)` and on a windows system use `(ctrl + enter)` for running an `individual line of code.`

Once you have the run the above code, there's an environment section to the right of your screen that will behave in a similar fashion as variable explorer in spyder.

In the next article, we'll look to `manage pre-processing phase of some missing data.`