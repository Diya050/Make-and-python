# Make-and-python

Make is called a build tool - it builds data files, plots, papers, programs or libraries. It can also update existing files if desired.

SetUp:
Checked if I already have Make installed by $ make -v. Make was already there. Make is a standard tool on most Linux systems and should already be available.

Python3 was already there.
Installed Numpy through $ sudo apt install python3-numpy successfully.
Installed Matplotlib using $ sudo apt-get install python3-matplotlib successfully.

# Introduction:
## Why to use Make?

The article discusses a pipeline for analyzing text files of a book using Python scripts as an example. The pipeline involves reading data files, performing analysis, writing analysis results to a new file, plotting a graph of analysis results, saving the graph as an image, and making a summary table of the analyses.

>Pipelining is the process of storing and prioritizing computer instructions that the processor executes.

Running python scripts at the shell prompt, as we have done, is fine for one or two files. If, however, we had 5 or 10 or 20 text files, or if the number of steps in the pipeline were to expand, this could turn into a lot of work. Plus, no one wants to sit and wait for a command to finish, even just for 30 seconds.

The most common solution to the tedium of data processing is to write a shell script that runs the whole pipeline from start to finish. There are two options for doing so. But these can be time-consuming and repetitive.

So it is recommended to use build tools like Make, which can automate the process and ensure reproducibility.
Make’s fundamental concepts are common across build tools.
Make allows us to specify what depends on what and how to update things that are out of date.

# Make-files:

    Downloaded make-lesson.zip.
    Moved make-lesson.zip into a directory which we can access via our bash shell.
    Navigated to the directory where we downloaded the file in terminal.
    Run   $ unzip make-lesson.zip

    $ cd make-lesson

    $ nano makefile 

    # Count words 
    isles.dat : books/isles.txt
    	python countwords.py books/isles.txt isles.dat

    $ make

    $ head -5 isles.dat

    $ touch books/isles.txt

    $ ls -l books/isles.txt isles.dat   

(  If we compare the timestamps of books/isles.txt and isles.dat,  then we see that isles.dat, the target, is now older than books/isles.txt ) 

$ make
(It recreates isles.dat) 

When it is asked to build a target, Make checks the ‘last modification time’ of both the target and its dependencies. If any dependency has been updated since the target, then the actions are re-run to update the target.

up to date means that the Makefile has a rule with one or more actions whose target is the name of a file (or directory) and the file is up to date.

Nothing to be done means that the file exists but either :

    the Makefile has no rule for it, or
    the Makefile has a rule for it, but that rule has no actions
# Makefiles as Documentation:

A Makefile is a file executed by Make, which describes how to build a target file using dependencies and actions, forming a rule.
By explicitly recording the inputs to and outputs from steps in our analysis and the dependencies between files, Makefiles act as a type of documentation, reducing the number of things we have to remember.

Adding another rule to the end of Makefile:

    abyss.dat : books/abyss.txt
    python countwords.py books/abyss.txt abyss.dat

    abyss.dat is a target, a file to be created, or built.

    books/abyss.txt is a dependency, a
    file that is needed to build or update the target. Targets can have
    zero or more dependencies.

    A colon, :, separates targets from dependencies.
    python countwords.py books/abyss.txt abyss.dat is an action, a command to run to build or update the target using the dependencies. Targets can have zero or more actions. These actions form a recipe to build the target from its dependencies and can be considered to be a shell script.

If we run Make,  nothing happens because Make attempts to build the first target it finds in the Makefile, the default target, which is isles.dat which is already up-to-date. We need to explicitly tell Make we want to build abyss.dat:  $ make abyss.dat

And we get:
python countwords.py books/abyss.txt abyss.dat

Adding to our Makefile:

    .PHONY : clean
    clean :
    	rm -f *.dat

Make finds a file (or directory) called clean and, as its clean rule has no dependencies, assumes that clean has been built and is up-to-date and so does not execute the rule’s actions. We are using clean as a short-hand, we need to tell Make to always execute this rule if we run make clean, by telling Make that this is a phony target, that it does not build anything. This we do by marking the target as .PHONY .

Phony target:
    A target that does not correspond to a file or other object. Phony targets are usually symbolic names for sequences of actions.


We can add a similar command to create all the data files. We can put this at the top of our Makefile so that it is the default target, which is executed by default if no target is given to the make command:

    .PHONY : dats 

    dats : isles.dat abyss.dat


This is an example of a rule that has dependencies that are targets of other rules. When Make runs, it will check to see if the dependencies exist and, if not, will see if rules are available that will create these. If such rules exist it will invoke these first, otherwise Make will raise an error. 

If we run,

$ make dats

then Make creates the data files:

    python countwords.py books/isles.txt isles.dat
    python countwords.py books/abyss.txt abyss.dat
    
