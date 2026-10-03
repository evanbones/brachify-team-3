# Virtual environments for *brachify*

*Last updated: 2026-10-01*

The following works on Windows, macOS (Apple Silicon and Intel), and Linux, using Visual Studio Code (VS Code) and conda 24.11.3 or newer.

**Important:** We only use conda for managing environments for *brachify*. Never run `pip install packagename` because mixing conda and pip is known to cause problems.

## Spec files
Conda spec files only work on the operating system they were made for, so there is one per platform. Use the one that matches your computer:

| Computer | Spec file |
|---|---|
| Windows | `spec-file-win-64.txt` |
| Mac with Apple Silicon (M1, M2, ...) | `spec-file-osx-arm64.txt` |
| Mac with Intel processor | `spec-file-osx-64.txt` |
| Linux | `spec-file-linux-64.txt` |

Not sure which Mac you have? Run `uname -m` in Terminal: `arm64` means Apple Silicon, `x86_64` means Intel.

`spec-file-win-64.txt` is the reference environment. The other spec files are generated from it by `generate_spec_files.py`, so all platforms use the same versions of the main packages.

## To create an environment:
    Overview:  
    - create from the spec file for your platform
    - this ensures that you use the correct versions of the libraries and packages

1. Download and install Anaconda (https://www.anaconda.com/download/success).
2. Clone brachify onto your computer. (Location: ~/path_to_folder/brachify)
3. Open a command prompt (Windows) or terminal (macOS/Linux), go to the brachify folder, and run the following command:  
`conda create --name name_of_env --file spec-file-PLATFORM.txt`  
replacing `name_of_env` with the desired name of your environment, and `spec-file-PLATFORM.txt` with the spec file for your platform (see [Spec files](#spec-files)).
4. Activate the newly created conda environment, by running the following in the command line:  
`conda activate name_of_env`, replacing `name_of_env` with the environment name.
5. Double-check that the correct conda environment is activated, by inspecting the list of conda environments, by running the following in the command line.  The currently activated one will have a star (*) beside its name.  
`conda info --envs`  
6. In VS Code, select this environment as your interpreter (bottom right of window, see reference below for exact instructions).  Now the code will be run with this environment.  

References:  
[1] https://docs.conda.io/projects/conda/en/24.11.x/user-guide/tasks/manage-environments.html#building-identical-conda-environments  
[2] https://code.visualstudio.com/docs/python/environments#_select-an-environment  
[3] https://docs.conda.io/projects/conda/en/24.11.x/user-guide/tasks/manage-environments.html#determining-your-current-environment  
[4] https://docs.conda.io/projects/conda/en/24.11.x/user-guide/tasks/manage-environments.html#activating-an-environment  



## To modify or update your environment:

### To add a new package:
    Overview:  
    - edit the environment.yml file by adding the new package
    - perform a test environment creation using the updated environment.yml to ensure that there are no problems
    - test brachify in the new environment to ensure no errors
    - create new spec files
    - tell everyone to create a new environment using the new spec file for their platform

1. Open the environment.yml file and add the new package to the list of dependencies. For example, add the `pandas` package.  If you want a specific version of the package, include it: `pandas=1.2.3`

**Before**:
```
dependencies:
  - python=3.12
  - matplotlib
```
**After**:
```
dependencies:
  - python=3.12
  - matplotlib
  - pandas
```

2. Perform a test run: create a new environment using the environment.yml:
    1. modify the environment name at the top of the file: `name: test_env`
    2. Run the following in the command line:  
    `conda env create -f environment.yml`  
    This creates a conda environment with the name specified in the environment.yml file, and installs all of the required packages (with the latest compatible versions) that are listed in the `environment.yml` file.  
    3. Activate the newly created conda environment, by running the following in the command line:  
    `conda activate name_of_env`, replacing `name_of_env` with the environment name (as specified in the environment.yml file).
    4. Double-check that the correct conda environment is activated, by inspecting the list of conda environments, by running the following in the command line.  The currently activated one will have a star (*) beside its name.  
    `conda info --envs`
2. Test *brachify* to ensure that no errors occur with the new environment.
2. Once you have verified that everything works as expected, replace your working environment with an updated version:
    1. remove the old one: `conda env remove --name name_of_env`
    2. restore the desired environment name in the environment.yml file
    3. follow the steps above to create the environment.
3. Update the spec files:
    1. on Windows, with the new environment activated, run: `conda list --explicit > spec-file-win-64.txt`
    2. then run: `python generate_spec_files.py`  
    This creates the spec files for the other platforms using the same package versions as the Windows spec file. Solving can take several minutes per platform. If a platform fails to solve, the version of one of the packages may not be available for that platform.
    3. run: `python generate_spec_files.py --check`  
    This checks that the spec files match `spec-file-win-64.txt`.
    4. Commit all four spec files together.

Everyone on the team should re-build their environments using the updated spec file for their platform (see [above section](#to-create-an-environment))


### To update to newer versions of the packages:
    Overview:  
    - if the package you want to update has its version specified in the environment.yml file, then change the specified version. If the package does not have its version specified, then conda will determine the newest version that is compatible with all the other packages in the file.
    - perform a test environment creation using the environment.yml
    - test brachify in the new environment to ensure no errors
    - create new spec files
    - tell everyone to create a new environment using the new spec file for their platform

1. Follow the steps [above](#to-add-a-new-package) for adding a package, but with the following modifications:
    1. If you want to update a specific package that already has its version specified in the environment.yml file, then edit the environment.yml file to have the new desired version. Follow all the other steps without modification.
    2. If you want to update the packages that do not have their versions specified, do not modify the environment.yml. 
4. In either case, conda will update all packages to their latest compatible versions, while also obeying the specified versions.

References:  
[1] https://docs.conda.io/projects/conda/en/24.11.x/user-guide/tasks/manage-environments.html#creating-an-environment-from-an-environment-yml-file  
[2] https://docs.conda.io/projects/conda/en/24.11.x/user-guide/tasks/manage-environments.html#create-env-file-manually  
[3] https://docs.conda.io/projects/conda/en/24.11.x/user-guide/tasks/manage-environments.html#activating-an-environment  
[4] https://docs.conda.io/projects/conda/en/24.11.x/user-guide/tasks/manage-environments.html#viewing-a-list-of-your-environments  
[5] https://docs.conda.io/projects/conda/en/24.11.x/user-guide/tasks/manage-environments.html#removing-an-environment  
[6] https://docs.conda.io/projects/conda/en/24.11.x/user-guide/tasks/manage-environments.html#building-identical-conda-environments  

## Debugging:

### InvalidArchiveError
**Description:** when trying to build the environment from the environment.yml file, if you get the following error:  
`InvalidArchiveError("Error with archive C:\\Users\\username\\AppData\\Local\\anaconda3\\pkgs\\viskores-1.1.1-cpu_h4b717ef_0.conda.  You probably need to delete and re-download or re-create this )`  
**Solution:** run the following command:  
`conda clean --all`  
This will remove all cached data while leaving your environments unchanged.  Try to build the environment again and the problem should be resolved.

References:  
[1] https://github.com/conda/conda/issues/12235  
[2] https://docs.conda.io/projects/conda/en/24.11.x/commands/clean.html  

### *brachify* crashes without error message on pdf export after creating environment from environment.yml
**Description:** after creating a new environment from the environment.yml file, when you run *brachify* and try to export the Reference Sheet pdf, *brachify* crashes and closes with no error message shown in the log or during debugging.  
**Solution:** delete the new environment. Run the following command:  
`conda clean --all`  
This will remove all cached data while leaving your environments unchanged.  Try to build the environment again and the problem should be resolved.

### pythonocc-core versions above 7.7.2
We have not been able to create a working environment with pythonocc-core above version 7.7.2.  
*(as of 2026-06-03)*