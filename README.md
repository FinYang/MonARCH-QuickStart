
# Fin's MonARCH Quick Start Guide

This repository aims to help beginners to start using MonARCH
efficiently. The target audience is someone who doesn’t have experience
with Slurm or similar workload managers. This repo should serve as a
complementary resource to the [official
documentation](https://docs.monarch.erc.monash.edu/) rather than a
substitute.

The information provided here is based solely on my personal experience and reflects my own approach to using MonARCH. Whenever in doubt, refer back to the official documentation of [MonARCH](https://docs.monarch.erc.monash.edu/) and [Slurm](https://slurm.schedmd.com/documentation.html).

I use R, so I have R in mind if I need to give any examples. The
principle and any command line code should still apply if you use other
languages.

## Tools

These are the tools I use alongside MonARCH.

- [Cyberduck](https://cyberduck.io/): Cloud storge browser that allows you to manage files on the clusters
- [Tabby](https://tabby.sh/): Terminal interface that can save ssh connection details.


## Configuration 

One can start an R session on the login node, but this is recommended against for any time/resources consuming task. We only use an R session on the login node here to install packages and manage directory. 

Use `module avail` to find the most recent version of R and load R using `module load [R with version]`:
``` bash
module load R/4.2.2-mkl 
R
```
Then simple entering `R` will start the R session. 

Since the module needs to be loaded everytime we start a new ssh connection to MonARCH server, it is easier to do this automatically in `.bashrc` file. `.bashrc` file exists in the root directory. By adding `module load R/4.2.2-mkl` to it, R will automatically be loaded. 
`.bash_profile` has a similar function. Instead of code that is automatically run, it contains global configuation variables that needs to be specified in each session. For now we will add one line
``` bash
export R_LIBS=~/R/libs
```
This basically says the library path for R is the `R/lib` folder in the root. Restart the ssh connecion and open an R session (with just command `R`), we can check the library path with
```R
.libPaths()
```
and you will see the path has been specified for you. 

In this R session, pacakge installation (from CRAN) is done in the same way as any regular R session with
```R
install.packages("tidyverse")
```
If you are used to the `Update` button in Rstudio, you might find the equivalent command below helpful when Rstudio is not available.
```R 
utils::update.packages("~/R/libs", ask = FALSE)
```

## Project management

Each project on MonARCH will receive it's own folder. The link to that folder will be in the root directory. The content in the project folder is accessible by any collaborator of this project, unless a folder is created for each user, which is recommended by the official document. I like to skip the user folder because I am usually the only one using MonARCH in the project and even if there are others, the code or results are probably need to be shared anyway. I will however creates folders around different themes and tasks in the project folder. It is unlikely a project only contain a single task and some level of separation is needed for clearity. As an example this is the struction of one of my project folder:
```
+ ab12
  + tourism
    + RandomForest
    + ForeCA
  + simulation
    + components
```
Here I am grouping different tasks by source of data. I have two tasks (`RandomForest` and `ForeCA`) using the `tourism` data and one task (`components`) using in `simulation`. 

Locally, I like to have my folders orgnaised exactly the same way. In my R prject, I will have a folder called `monarch` and have the same structure as `ab12` in the above example. This way I can [sync the files] to keep an exact copy locally. That makes it easier to remind myself how the files are organised when I come back to a project I haven't touched in a while. And I do my version control locally on my device, not directly from the server. 
```
+ monarch
  + tourism
    + RandomForest
    + ForeCA
  + simulation
    + components
```

## Inside a task folder

### README
I always start will a `README.md` file. Documentation is important. I have atempted to use MonARCH a few times but I couldn't produce consistent meaningful results because I always forget how I organised things after a while - until I start to write README.

Below is a typical example of my README file

```` markdown
# ab12/tourism/RandomForest on MonARCH

This folder contains the code to [insert task]

Use code below to sync between local folder and server (run from local device).
```
# local to remote
rsync --progress -auv --chmod=ugo-rwX -e ssh monarch/tourism/RamdomForest username@monarch-dtn.erc.monash.edu:~/ab12/tourism/  --exclude output -h -h
# remote to local
rsync --progress -auv -e ssh username@monarch-dtn.erc.monash.edu:~/ab12/tourism/RamdomForest monarch/tourism --exclude slurm_output --exclude slurm_error -h -h
```

The directory structure on MonARCH is planned to be:
```
ab12
+ tourism/RandomForest
+ data (folder containing data)
+ functions (R functions)
+ output (folder containing the output data)
+ slurm_output (folder containing slurm output log files)
+ slurm_error (folder containing slurm error files)
- job.sh
- do
```

Submit jobs with:
```
# Job name
sbatch ab12/tourism/FOrest/job.sh
```
````
