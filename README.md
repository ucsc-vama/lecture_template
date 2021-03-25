# chisel-nbdev template
Use this template to more easily create your [chisel_nbdev](https://github.com/ucsc-vama/chisel_nbdev) project.

- Note: This repo is a fork of the [nbdev_template](https://github.com/fastai/nbdev_template) repo.

A detailed explantation to get started can be found from the original nbdev documentation: [here](https://nbdev.fast.ai/tutorial.html#Set-up-Repo)

## Installation:
- Create your own repo using this template repo by clicking `Use this template`.
- git clone your repo to your local machine and `cd` into it. 
- If on Mac/Linux and `virtualenv` is installed, run `./install.sh` to automatically complete the rest of the steps in this section.
- setup some virtualenv (im using python3.8.2)
  - `pip3 install virtualenv`
  - find your python3 binary: `which python3` which for me is `/usr/local/bin/python3`
  - create the virtualenv`python3 -m virtualenv --python=/usr/local/bin/python3 chisel_nb_env`
- activate virtualenv: `source chisel_nb_env/bin/activate`
- install chisel-nbdev: `pip install -U chisel-nbdev`
- install Jupyter: `pip install jupyterlab`

- Setup the Jupyter Scala kernel Almond (https://almond.sh) 
  - Borrowed from the Chisel-Bootcamp install guide: https://github.com/freechipsproject/chisel-bootcamp/blob/master/Install.md
  - If you experience errors or issues with this section, try running `rm -rf ~/.local/share/jupyter/kernels/scala/` first.
  - Next, download coursier (our dependency manager) and use it to install almond (our kernel wrapped around Ammonite) (see here for the source for these instructions):
  ```
  curl -L -o coursier https://git.io/coursier-cli && chmod +x coursier
  SCALA_VERSION=2.12.10 ALMOND_VERSION=0.9.1
  ./coursier bootstrap -r jitpack \
      -i user -I user:sh.almond:scala-kernel-api_$SCALA_VERSION:$ALMOND_VERSION \
      sh.almond:scala-kernel_$SCALA_VERSION:$ALMOND_VERSION \
      --sources --default=true \
      -o almond
  ./almond --install
  ```
  - You can delete coursier and almond files if you so desire.

### Edit settings.ini
- By default Scala scripts generated from notebooks are placed in the `lib/` folder. If a more specific library name is desired edit `settings.ini`:
> ```
> lib_name=<your_name>
> ```
- For more info on the other settings see the original `nbdev` [here](https://nbdev.fast.ai/tutorial.html#Set-up-Repo).

### Export Notebooks To Scripts
- Using the `chisel_nbdev_*` command line functions require you to be in the virtualenv context where you pip installed chisel-nbdev. The full list of commands are documented [here](https://ucsc-vama.github.io/chisel_nbdev/cli.html).
- Running `chisel_nbdev_build_lib` will export tagged cells from Almond notebooks into Ammonite scripts which can be imported into other notebooks.
- The first occurence of a cell tagged with `//default_exp <module_name>` will set the destination script.
- Scripts are written to the folder specified by `lib_name` in `settings.ini`.
- See `nbs/00_parent.ipynb` and `nbs/01_child.ipynb` for an example of importing code between notebooks.
 
### Start JupyterLab
- Making sure you have activated your virtualenv then run in terminal: ```jupyter lab ```. 
  - Note we will still need to use the virtualenv in the terminal to run command line functions. To avoid needing a second terminal window open, you can run ```jupyer lab &``` to run the server in the background. 
- Open your browser to the link specified in your terminal by JupyterLab.
- New notebooks can be added to the `nbs` folder through the `JupyterLab` IDE.

### Propagate changes from scripts to notebooks
- If code is added within cell boundaries, and no cells are added or removed, `chisel_nbdev_update_lib` will propagate the changes back to the source notebook file.

### Run tests from command line
- `chisel_nbdev_test_nbs` will run all of the tests (cells not containing `//export`) from the command line. (currently very slow)

## Troubleshooting Tips
- If `01_child.ipynb` does not work in JupyterLab it is likely the file `parent.sc` was never generated via `chisel_nbdev_build_lib`.
- If `01_child.ipynb` fails when running `chisel_nbdev_test_nbs` it is likely `lib_name` was changed from default `lib` and the ```import $file.^.lib.parent, parent._``` needs to be update with the new `lib_name`.
-  Make sure you are using the latest version of chisel-nbdev with `pip install -U chisel-nbdev`
-  It is important for you to spell the name of your user and repo correctly in `settings.ini` or the website will not have the correct address from which to source assets like CSS for your site.  When in doubt, you can open your browser's developer console and see if there are any errors related to fetching assets for your website due to an incorrect URL generated by misspelled values from `settings.ini`.
-  If you change the name of your repo, you have to make the appropriate changes in `settings.ini`
-  After you make changes to `settings.ini`, run `chisel_nbdev_build_lib && chisel_nbdev_clean_nbs && chisel_nbdev_build_docs` to make sure all changes are propagated appropriately.
-  `chisel_nbdev_build_docs` should be run from within the `nbs` folder.
-  In order to propagate changes between notebooks, the Ammonite scripts must be re-exported via `chisel_nbdev_build_lib` and the kernel of the destination notebook must be restarted to import the updated script.

