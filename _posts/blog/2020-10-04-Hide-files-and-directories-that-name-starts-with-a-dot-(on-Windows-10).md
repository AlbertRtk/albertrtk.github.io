---
author: albert
tags: task-automation windows
---
On the Linux system, files and directories which name starts with a dot are often used for storing settings, etc. They are "hidden" in many file managers. The convention is often transferred  to Windows, however Windows Explorer doesn't hide such named files and directories. Which annoyed me...
<!--more-->

So, I started to search for a solution and I discovered ([here](https://superuser.com/questions/364406/hiding-files-folders-which-begin-with-a-full-stop-period)) ```attrib``` command. The command is documented [here](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/attrib). To hide in the Windows Explorer all files and directories which name starts with a dot and which are located in my user's directory I used the following command
```bash
ATTRIB +H /d C:\Users\my_user\.*
```
I typed it in the Command Prompt, hit ```ENTER```, and done! All dotted files/directories are hidden now. However, if a new directory or file with a name starting with a dot will appear in my folder, it won't be hidden automatically. In order to solve this issue, I've written a simple python script, which now runs in the background on my machine and takes care to hide all files and directories which name starts with a dot - the code below.

```python
import subprocess
from os.path import expanduser, join
from time import sleep


def hide_dotted(dir_path): 
    command = r'ATTRIB +H /d ' + join(dir_path, '.*')
    subprocess.Popen(command, 
                     creationflags=subprocess.CREATE_NO_WINDOW)


if __name__ == '__main__':
    
    DIRECTORY = expanduser("~")  # User's dir
    SLEEP_TIME = 10

    while True:
        hide_dotted(DIRECTORY)
        sleep(SLEEP_TIME)
```

The script runs an infinite while loop and every 10 seconds calls the function ```hide_dotted``` for my user's directory. The function takes care of the rest, creates the command and assigns the hidden attribute to all files and directories which name starts with a dot. 

If you find the script useful, feel free to [download it](https://github.com/AlbertRtk/windows_10_scripts/blob/main/hide_dotted.pyw), and adjust to your needs.

If you would like the script to start with your system, press ```Win + R``` and run ```shell:startup```. Place a shortcut to the script in the directory which opens. Ready! Next time when you will start your PC the script will run too.
