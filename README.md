# FreeCAD macOS (Apple Silicon) Build Notes

This is a brief guide to checking out, building, and debugging FreeCAD on macOS tested on Apple Silicon using Visual Studio Code and Conda to provide a consistent development environment.

## Building FreeCAD

1. Install mamba

        brew install cmake ninja mambaforge
        conda init "$(basename "${SHELL}")"
        source ~/.zshrc

2. Clone FreeCAD

        git clone https://github.com/oursland/FreeCAD.git -b conda-env

3. Install conda environment for FreeCAD

        conda env create -p .conda/freecad -f .vscode/conda-env.yaml
        conda config --set env_prompt '({name})'
        source activate freecad

4. Install netgen

    The current netgen is an older release that did not support Apple Silicon.  There is currently a [pull request](https://github.com/conda-forge/netgen-feedstock/pull/44) open to update to the latest release.  Until that pull request is merged in, we will need to install netgen from source.

        git clone https://github.com/oursland/netgen-feedstock.git -b personal/joursland/apple-silicon
        cd netgen-feedstock
        conda build .
        conda install [built tarball]

5. Open FreeCAD in VS Code

    First we need to select our toolchain.  Use the CMake Kit button in the status bar or run the "CMake: Select a Kit" command to choose the "FreeCAD Clang" toolkit.

    Next we will select the build variant.  Use the CMake Variant button in the status bar or run the "CMake: Select Variant" command to choose "Debug".

    This should now configure CMake to build FreeCAD.

6. Build FreeCAD

    Use the `F7` key or run the `CMake: Build` command

## Running and Debugging FreeCAD

Visual Studio Code has been configured with two debug configurations:

* `C/C++: clang++ build and debug FreeCAD`
* `Python: Remote Attach`

![Debug Contexts](images/debug-contexts.png)

The following keys and commands execute common debugging tasks:

* `F5` : `Debug: Start Debugging`
* `Ctrl-F5` : `CMake: Run Without Debugging`
* `F11` : `Debug: Start Debugging and Stop on Entry`

### Debugging C++

The C++ debug configuration proves useful for developing the core FreeCAD application as well as workbenches that may rely upon C++ libraries.

All debugging functionality including breakpoints, variable inspection, register inspection, watch expressions, and call stack investigation are fully operational.

One thing to note is that when one runs the `Pause` (`F6`) operation, there may be considerable delay before the debugger halts execution to permit inspection.

![Debugging C++](images/debug-cpp.png)

### Debugging Python

FreeCAD has integrated functionality to connect to a remote python debugger using the `ptvsd` tooling present within Visual Studio Code.  

To debug python code:

1. Launch FreeCAD with either `Start Debugging` (`F5`) or `Run Without Debugging` (`Ctrl-F5`)
2. In FreeCAD's menu select `Macro`->`Attach to remote debugger...`

    ![Macro](images/python-debug-macro.png)

3. Select the tab `VS Code` and click the `OK` button.

    ![Attach](images/python-debug-attach.png)

4. In VS Code's `Run and Debug` page, select the `Python: Remote Attach` configuration.

    ![VS Code](images/python-debug-vscode.png)

5. Click `Start Debugging` (`F5`)

When launching from within VS Code and then debugging Python, you will end up with two debug contexts.  In the floating debug bar, the context will be a drop-down box on the right.  Be sure to select the correct context for the action you wish to execute.

When debugging python, a `Pause` (`F6`) operation will occur when the next Python code is executed.  To trigger this, interact with an element in FreeCAD such as the workbench drop-down menu.

![Debug Bar Contexts](images/debug-bar-contexts.png)
