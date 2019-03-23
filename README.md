# NotebookScheduler
A simple script to help schedule Jupyter Notebook execution and storing of the results using Papermill

Check out [this blog post](https://productmetrics.net/blog/schedule-jupyter-notebooks/) for more details.

## Introducing NotebookScheduler

[NotebookScheduler](https://github.com/Joshuaek/NotebookScheduler) is a simple Python script which uses [Papermill](https://github.com/nteract/papermill) to execute a directory of Jupyter Notebooks. Notebooks are arranged into subfolders for hourly, daily, weekly or monthly execution. Each time a notebook is run, a snapshot is saved to a timestamped folder (along with any other outputs your notebook saves) giving you the ability to look back at past executions and to have a full audit of the analysis that has been done.

Once I've set up the notebook to provide whatever stats I want, scheduling its execution on a weekly basis is now as simple as a drag-and-drop  into the weekly subfolder.

## Getting started

The code is available in this [GitHub repository](https://github.com/Joshuaek/NotebookScheduler) -clone or download it to a folder on your PC. The first time you run the script, it will create a skeleton directory structure, with subdirectories for hourly, daily, weekly and monthly notebooks.

Simply move your notebook (*.ipynb) files into the relevant subdirectory and when the script is run they will be executed.

The directory structure is shown below:

```
 <script_folder>/
 ├── NotebookScheduler.py 
 ├── hourly/
 │   ├── notebook1.ipynb
 │   ├── notebook2.ipynb
 │   └── snapshots/
 │       ├── notebook1/
 |       │   └──<timestamp>
 │       │      └── notebook1.ipynb
 │       └── notebook2/    
 |           └──<timestamp>
 │              └── notebook2.ipynb 
 ├── daily/
 │   ├── notebook3.ipynb
 │   ├── notebook4.ipynb
 │   └── snapshots/
 │       ├── notebook3/
 |       │   └──<timestamp>
 │       │      └── notebook1.ipynb
 │       └── notebook4/    
 |           └──<timestamp>
 │              └── notebook2.ipynb 
 └── weekly/
     ├── notebook5.ipynb
     ├── notebook6.ipynb
     └── snapshots/
         ├── notebook5/
         │   └──<timestamp>
         │      └── notebook1.ipynb
         └── notebook6/    
             └──<timestamp>
                └── notebook2.ipynb 
```

## Install the dependencies

The script has a few dependencies.

### Papermill

[Papermill](https://github.com/nteract/papermill) is the module that runs the jupyter notebooks. You'll need to install Papermill and its dependencies first.

``` 
pip install papermill 
```

### Schedule

If you want to use the built in scheduler, then you'll need to install [Schedule](https://pypi.org/project/schedule/).

``` 
pip install schedule 
```

If you're going to use Windows Task Scheduler or Cron jobs to schedule the execution, then you don't need this. 

## Running the script without an external scheduler

The simplest way to get started is to use the built in scheduler. In this mode, you'll run the Python script in a terminal and leave it running. The script itself will loop and run the notebooks as per the schedule determined by which of the subdirectories the notebook is in (e.g. daily, weekly).

To do this, once you have some notebooks in your folders, simply run the script from its root folder:

``` 
python NotebookScheduler.py 
```

## Running the script with an external scheduler

An alternative way of running is to use an external scheduler, like the built in Windows Task Scheduler or a Cron job to execute the script. In this mode, the external scheduler will determine the frequency of execution. You just need to set the ```-d``` command line option to tell the script which directory to execute. So, if you wanted to run your hourly and daily scripts, you'd set up two tasks:

One job set to run hourly, with the script executed as follows:

```
python NotebookScheduler.py -d hourly
```

And another one set to run daily, with the script executed as follows:

```
python NotebookScheduler.py -d daily
```

When the directory is specified using the ```-d``` option, the notebooks in the specified directory are executed immediately.

## About the snapshots

Within each of the daily/hourly/weekly/monthly directories a "snapshot" directory will be created. This will have sub-folders for each notebook that is executed, and each execution will be stored in time stamped folder. Whilst this is a lot of nesting, it makes it quick and easy to view the output of a particular notebook on a particular day. Once the notebook is executed, Papermill will save the output notebook to the snapshot directory.

## Saving other artifacts

Papermill can [pass parameters](https://papermill.readthedocs.io/en/latest/usage-parameterize.html)  to the notebooks it is executing. NotebookScheduler will set a ```snapshotDir``` parameter so that you can use this within your notebooks for saving files within the snapshot directory. For example, the following code generates a random dataframe and then saves a .csv file into the snapshot directory. This means that each execution of the notebook has it's .csv output right next to the output notebook in the timestamped folder.

```python
import pandas as pd
import numpy as np
import random

snapshotDir = ""

df = pd.DataFrame(np.random.randint(0,100,size=(100, 4)), columns=list('ABCD'))

df.to_csv(snapshotDir + 'output.csv')
```

Hopefully that helps keep everything neat and tidy!

## Logging

Logging is setup - once the script is run you'll see ```notebook.log``` appear in the folder. All executions are logged here. If anything goes wrong with the execution (e.g. somethings broken in your notebook) then a stacktrace will be included in the log. All actions are logged to a single log file so you only have one place to check to see if scripts have run or find out why they broke.

## Testing and feedback

I've only tested the script using Python 3.6 so far. If you encounter any bugs or strange behaviour then please raise an issue via the [repository](https://github.com/Joshuaek/NotebookScheduler).