## What is this?

The `postBuild` script allows us to alter the template used to render files in
the Jupyter notebook interface. We use it to add Google Tag Manager for
analytics, add usage tracking, add our logo, and change the color scheme in the
code cells.

Since each notebook container (a server inside a server) is built based on the
files in the notebook's repo, and the `postBuild` script needs to run from
inside the container, we need to add this script to every notebook's repo.

Authors may have their own `postBuild` script which they would probably use to
add notebook extensions. Notebook extensions add additional features to the
Jupyter notebook user experience.

The program that builds the notebook container looks for a `postBuild` script
in the following locations, in the order shown:

1. `[repo root]/binder`
2. `[repo root]/.binder`
3. `[repo root]`

After our `postBuild` script runs, it removes itself from the container so as not to confuse
notebook users (it remains in the repo).


## How to add the postBuild script to a notebook repo

If the author **does not** already have a `postBuild` script of their own:

1. Copy this postBuild script into the notebook's repo. If the notebook has a
   `binder` folder, it must go there. Otherwise if it has a `.binder` folder,
   it must go there. Otherwise it should be at the repo root.
2. Adjust line 3 of notebook repo's `postBuild` file so that
   `CONTEXT_IDENTIFIER='666333TESTNTBK6633'` contains the correct identifier
   (instead of `666333TESTNTBK6633`). 
   * For a **live training notebook** this
   should be the **course ID.** 
   * For a regular **content notebook**, this should be the
   **notebook FPID.**
3. Adjust line 4 so that `NOTEBOOK_FPID='666333TESTNTBK6633'` contains
   the notebook FPID. **This should be the notebook FPID for both kinds of
   notebooks.** This will seem redundant in content notebooks, but it's
   necessary for tracking usage.


### Additional steps if the notebook repo has a Dockerfile

First, add these two lines to the end of the Dockerfile:

```
COPY ./postBuild /home/$NB_USER/postBuild
RUN /home/$NB_USER/postBuild
```

Then, make sure the `postBuild` script is executable.  So, if you copied and
pasted the contents into a new file, you need to run `chmod +x
/path/to/postBuild` in a terminal. If you aren't comfortable with the terminal,
then you should clone this repo and copy the actual file into the notebook
repo.


## What if the author already has a postBuild file in the repo?

Since the `postBuild` script is just a list of commands, we can combine them.

1. Rename the author's `postBuild` script to `postBuild.original`.
2. Follow the steps in the above section. You shouldn't need to do anything
   special if the repo has a Dockerfile, since the author will have already set
   it up to run their own `postBuild` script.
3. Add the contents of the author's `postBuild` script to the top of our script
   (beneath the `#!/bin/bash` line).

When the `postBuild` script runs, after it removes itself from the container, it looks for a file
in the same directory called `postBuild.original` and renames it `postBuild`.
It's like we were never there!

