# Introduction

Whilst your data may be held in an Excel-format/database, it is worth using the power of Git and version control to ensure your team works together successfully when making updates, as well as ensuring there is a central repository where all revisions can be viewed since the start of the R-based project.

Thankfully, this repo has already been set up. However, this page will discuss how to clone (i.e. copy) the repo to your local machine so that you can make revisions there, set up version control and the pull/push process for any updates.

# Setting your proxy and config files
Before you can begin working locally on remotely-hosted files stored on Azure, you will need to ensure your system is 'linked' to Azure. For example, these Wiki pages are provided by the files stored in the Wiki folder within the 'Transitioning from Excel to R' repo, which in turn is linked to a version on my local system.

Firstly, you need to install Git, a version control system that will work in tandem with Rstudio - [Git for Windows](https://gitforwindows.org/)

You should then decide upon a folder where you will store your 'Repos'. I recommend storing it as close to or even within the main C:// drive folder e.g. `C:\Repos`. Within this folder, once Git for Windows is installed, you can right click and select 'Git Bash here'. This will open a git command window, focused on the Repos folder.

At the same time, you should set your config file settings. A config file acts with Git and Rstudio to connect through Azure and allow copies and modifications to be made of stored files. Without the config file, you will likely receive 'HTTP 407' or 'HTTP 503' connection errors. You can however do this easily within the Git bash window by typing:

```
git config --global http.sslVerify false
git config --global https.sslVerify false
git config --global http.proxy http://user:pass@mwg.proxy.ad.hq.dept:9090
git config --global https.proxy http://user:pass@mwg.proxy.ad.hq.dept:9090

```
This code will set up your basic config file to bypass the departmental proxy and allow you to download packages and repos going forward.

# Clone the repository

With the Git window open, you can now clone the 'Transitioning from Excel to R' repo. If you navigate to the Repo folder for the project [here](https://dev.azure.com/dfe-gov-uk/_git/Transitioning_from_Excel_to_R), you will see a 'clone' button.

[[img Wiki/images/clone.PNG alt=foobar]]

# 