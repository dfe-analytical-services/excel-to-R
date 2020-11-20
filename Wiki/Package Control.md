# Introduction

Over time due to the nature of R being open-source, packages will evolve. Often this will bring additional functionality, however, it may also stop some older functionality working if the developer chooses to remove or tweak that particular element.

[Renv](https://rstudio.github.io/renv/articles/renv.html) is a way of ensuring that R always looks/holds an older version of the relevant package, regardless of how much the package is changed over time. It does so by creating a 'lockfile' which references the version of R, the version of the package, and a hash key that identifies the package version uniquely. 

What this means is that your project creates its own repository of packages, entirely separate and untouched from the rest of your R installation. You may update ggplot2 for example in your R installation, but if you do not do the same when running in your project AND save that change to your renv lockfile, other users within the same project will use the version previously specified in the lockfile.

The link above covers how to install and run Renv, so this wiki is provided more as a tips and troubleshooting page.

# Tips 

- Ideally, wait until you are close to or have finished making the bulk of your development work. You can choose when creating an R project to track package versions with renv if you wish. Leaving it until the end will ensure that at the time of the app going live, you are a) dealing with the most up to date functionality available and b) ensures any issues with renv will not delay development.

- 


# Trouble shooting

- path. Need to set up the correct windows variable

- install.packages failing
  - Several options here:
  1) install.packages within the environment. 
  2) use the renv install.packages version
  3) Review the lockfile and update it to use the correct version of the package
  4) ***Nuclear option***: Delete the renv folder, transfer an older version of the renv folder to your system and attempt to do renv::restore().

- Repo length: renv can be temperamental when it comes to long filenames. If you have your project nested within 5 or 6 sub-folders, it is entirely possible that installation is failing/renv is failing because the filepath is above 256 characters. Move your repo to somewhere higher up in the C: drive!

- If the above fails, feel free to ask for help on [R shiny developers](https://teams.microsoft.com/l/channel/19%3a311ec2e4d46b4dd38f0d61f05fb93383%40thread.skype/General?groupId=b95c605d-8fbc-4e4d-9a76-2f7d1c55e70a&tenantId=fad277c9-c60a-4da1-b5f3-b3b8b34a82f9). This brings together users of R shiny and R within the department, so it is highly likely someone will have had the same issue or can guide you in the right direction with renv.