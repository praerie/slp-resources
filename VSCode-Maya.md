# VSCode - Maya Setup

## Connect VSCode to Maya

### Step 1: Software Installation

Install [Maya](https://www.autodesk.com/products/maya/overview) and [VSCode](https://code.visualstudio.com/download)!

### Step 2: VSCode Extension

In VSCode, open the Extensions tab, located on the left sidebar. Search for and install **MayaCode**, which sends Python and MEL code directly to Maya through a command port.

Maya ships with a special Python interpreter called `mayapy`, but you do not need to set this as your interpreter in VSCode. Instead, keep your system Python (e.g. Python 3.10) active in VSCode. MayaCode will handle sending your scripts to Maya directly. Setting mayapy as your VSCode interpreter is not recommended, as it can cause an endless "Reactiving Terminal" loop.

### Step 3: userSetup.py

We are going to use a userSetup.py script to make Maya run specific commands at start up. This script is automatically run when Maya launches. You can use it to open command ports, import custom modules, or extend Maya's behavior at startup.

**First,** locate your Maya scripts folder:

| OS | Scripts Folder Location |
|-|-|
| Windows | `%USERPROFILE%\Documents\maya\<version>\scripts` |
| Linux | `$HOME/maya/<version>/scripts` |
| macOS | `$HOME/Library/Preferences/Autodesk/maya/<version>/scripts` |

If you don't see a scripts folder at the listed path, open Maya's Script Editor and run `getenv("MAYA_APP_DIR")`. This will return the full path to your `MAYA_APP_DIR`. You can then navigate to `MAYA_APP_DIR/<version>/scripts` to create or edit your userSetup.py file.

**Next,** create a new file in your Maya scripts folder called `userSetup.py` with the following content:

```python
import maya.cmds as cmds

if not cmds.commandPort(':7001', q=True):
    try:
        cmds.commandPort(name=':7001', sourceType='python')
    except RuntimeError as e:
        print(f"Could not open commandPort on :7001 — {e}")
```

### Step 5: Test the connection

* Open Maya and click 'New Scene' from the launcher.
* In VSCode, create a new Python file called `sphere.py` in your working directory (e.g. desktop or project folder). Avoid placing it inside Maya's `scripts` folder, as that is reserved for startup scripts like `userSetup.py`.
* 

## (Optional) Autocompletion

Even after setting your interpreter to Maya's internal Python interpreter (`mayapy`), you might notice that autocomplete is minimal. This is because VSCode uses static analysis to generate autocomplete suggestions, but Maya's `maya.cmds` module is compiled and doesn't expose detailed function definitions by default. So even with the correct interpreter set, VSCode doesn't "see" enough information to offer help.

The solution? **Stubs!** If you're unfamiliar with stubs, they're lightweight files that include things like docstrings and type hints. They help VSCode understand what your code is doing so it can offer autocomplete and helpful suggestions.

### How to install the `maya-stubs` package by Muream:

In your system terminal or the integrated terminal inside VSCode, run the following command, replacing `/path/to/maya` with the actual path to your Maya's Python interpreter:  

    /path/to/mayapy -m pip install maya-stubs

_Note: This command will not work in the Maya Script Editor nor Python console._

Since you have already set `mayapy` as your interpreter per the setup instructions above, VSCode should automatically detect the change. If needed, restart VSCode or refresh the window by going to the Command Palette and selecting `Developer: Reload Window`.

Alternatively, if you'd like to inspect or tweak autocomplete behavior, you may manually download the stubs here:  
🔗 PyPI: [https://pypi.org/project/maya-stubs](https://pypi.org/project/maya-stubs)  
🔗 GitHub: [https://github.com/Muream/maya-stubs](https://github.com/Muream/maya-stubs)  

***

## Troubleshooting FAQ

### I'm seeing a port error in Maya: _Error: Unrecognized action "port localhost:7001."_



### "I was able to send my Python code to Maya and create a basic sphere per the instructions, but I'm seeing the message: _Import "maya.cmds" could not be resolved._

This warning is coming from your Python language server (usually Pylance in VSCode). It means your editor can't find the `maya` module in your current Python environment. This is expected because `maya.cmds` is only available through Maya's built-in Python interpreter, called `mayapy`, and not in your regular Python environment. Your code will still run fine when sent to Maya, so you can safely ignore the warning unless you want full autocomplete support (see Autocompletion above).

### "I installed Maya, but Bifrost and Arnold Renderer weren't installed successfully."

Your Python for Maya course will not require Bifrost nor Arnold. As long as your core Maya installation is working, you are good to go! If you'd like to explore these plug-ins outside of the Python for Maya course, you may find these links helpful:

🔗 [Bifrost Help](https://help.autodesk.com/view/BIFROST/ENU/?guid=Bifrost_MayaPlugin_install_bifrost_for_maya_html)  
🔗 [Arnold for Maya Installation](https://help.autodesk.com/view/ARNOL/ENU/?guid=arnold_for_maya_getting_started_am_Installation_html)

