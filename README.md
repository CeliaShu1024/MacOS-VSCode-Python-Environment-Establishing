This is a record of my MacOS VSCode installation and configuration for establishing python environment. It is firstly posted on CSDN Forum: https://blog.csdn.net/qq_41415890/article/details/126208376?spm=1001.2014.3001.5501
My system versions were `Monterey` and `Big Sur`. This method is verified by these two MacOS versions, but I don't know whether it's available for other MacOS versions. 
The software or extensions used in this methods are `terminal.app`, `Homebrew`, `zlib`, `pyenv`, `vim`, `PyPI` and `visual-studio-code`.
## Step 1: Install Homebrew
You can find the homebrew installation command in its official website: https://brew.sh/. 
Open `terminal.app`: Press `command`+`space` and type `terminal.app`, then press `enter`.
Copy and paste homebrew installation command in terminal shell:
```{Bash}
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
## Step 2: Use Homebrew Install Software
In this stage we need to install `zlib`, `pyenv`, `vim` and `visual-studio-code`.
For the first three software, it is only needed to type the following codes in terminal:
```{Bash}
brew install zlib
brew install pyenv
brew install vim
```
For `visual-studio-code`, we need to set up `brew-cask` extension at first:
```{Bash}
brew install homebrew/cask/brew-cask
brew install visual-studio-code --cask
```

## Step 3: `zlib` Configuration for Linking Homebrew
```{Bash}
brew link zlib --force
export LDFLAGS="-L/usr/local/opt/zlib/lib"
export CPPFLAGS="-I/usr/local/opt/zlib/include"
```

## Step 4: Use `pyenv` to Install Python3
Here I chose `Python@3.9`. You can choose any other version if you like.
```{Bash}
pyenv install 3.9.0
pyenv versions
pyenv global 3.9.0
```
After the above configurations, install `virtualenv` extension for `pyenv`:
```{Bash}
brew install pyenv-virtualenv
pyenv virtualenv 3.9.0 nenv390
```

## Step 5: Modify System Environment PATH
First, check the installation path of `Python@3.9`:
```{Bash}
pyenv which python3
```
The sample output of this command should be a path similar to `/Users/[user-name]/.pyenv/versions/3.9.0/bin/python3`.
Copy this path (ends at /bin), for example, `/Users/[user-name]/.pyenv/versions/3.9.0/bin`.
Then open the `.bash_profile`:
```{Bash}
sudo vim ~/.bash_profile
```
Press `i` to switch to edit mode. Add the following command in the end of `.bash_profile`:
```{Bash}
export PATH=/Users/[user-name]/.pyenv/versions/3.9.0/bin:$PATH
```
Press `esc` to quit edit mode, then press `:wq!` to force save and quit. Apply the changes by:
```{Bash}
source ~/.bash_profile
```

## Step 6: Modify Default Python Site-Packages PATH
First, check the current python site path by:
```{Bash}
python3 -m site
```
The sample output of this command might like:
```{Bash}
sys.path = [
    '/Users/[user-name]',
    '/Users/[user-name]/.pyenv/versions/3.9.0/lib/python39.zip',
    '/Users/[user-name]/.pyenv/versions/3.9.0/lib/python3.9',
    '/Users/[user-name]/.pyenv/versions/3.9.0/lib/python3.9/lib-dynload',
    '/Users/[user-name]/.pyenv/versions/3.9.0/lib/python3.9/site-packages',
]
USER_BASE: a path followed by (doesn't exist)
USER_SITE: a path followed by (doesn't exist)
```
To change these non-exsistent paths, we should check the current site PATH settings file by:
```{Bash}
python3 -m site -help
```
The sample output of this commmand might like:
```{Bash}
/Users/[user-name]/.pyenv/versions/3.9.0/lib/python3.9/site.py [--user-base] [--user-site]
 
Without arguments print some useful information
With arguments print the value of USER_BASE and/or USER_SITE separated
by ':'.
 
Exit codes with --user-base or --user-site:
  0 - user site directory is enabled
  1 - user site directory is disabled by user
  2 - user site directory is disabled by super user
      or for security reasons
 >2 - unknown error
```
The settings file is the first line of this output. In this case, it is `/Users/[user-name]/.pyenv/versions/3.9.0/lib/python3.9/site.py`. Change it by:
```{Bash}
sudo vim /Users/[user-name]/.pyenv/versions/3.9.0/lib/python3.9/site.py
```
or:
```{Bash}
cd ~
sudo ls
cd .pyenv
cd versions
cd 3.9.0
cd lib
cd python3.9
sudo open site.py
```
Drop the descriptions at the beginning and find the following contents in the top of python file body part:
```{Python}
# Prefixes for site-packages; add additional prefixes like /usr/local here
PREFIXES = [sys.prefix, sys.exec_prefix]
# Enable per user site-packages directory
# set it to False to disable the feature or True to force the feature
ENABLE_USER_SITE = False
 
# for distutils.commands.install
# These values are initialized by the getuserbase() and getusersitepackages()
# functions, through the main() function when Python starts.
USER_SITE = non-existent path
USER_BASE = non-existent path
```
Modify `ENABLE_USER_SITE`, `USER_SITE` and `USER_BASE`:
```{Python}
# Prefixes for site-packages; add additional prefixes like /usr/local here
PREFIXES = [sys.prefix, sys.exec_prefix]
# Enable per user site-packages directory
# set it to False to disable the feature or True to force the feature
ENABLE_USER_SITE = True
 
# for distutils.commands.install
# These values are initialized by the getuserbase() and getusersitepackages()
# functions, through the main() function when Python starts.
USER_SITE = "/Users/[user-name]/.pyenv/versions/3.9.0/lib/python3.9/site-packages"
USER_BASE = "/Users/[user-name]/.pyenv/versions/3.9.0"
```
The two paths could be found in the output of `python -m site`. After modifying these settings, save and quit.

## Step 7: Install PyPI
Install PyPI (pip) by the following commands:
```{Bash}
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.pysudo python3 get-pip.py
sudo python get-pip.pysudo
```

## Step 8: Configure VSCode
Open VSCode and download `Code Runner`, `Python Extension Pack` and `Pylance`.
Create a python file. After that, press `command`+`shift`+`P`, type `settings.json` and press `enter`.
Add the following contents in `settings.json`:
```{JavaScript}
{
    "python.defaultInterpreterPath":"/Users/[user-name]/.pyenv/versions/3.9.0/bin/python3",
    "python.analysis.extraPaths": [
        "/Users/[user-name]/.pyenv/versions/3.9.0/lib/python3.9/site-packages"
    ]
}
```
Save and quit `settings.json`.

## Step 9: Restart VSCode and Run Python Code
After restarting VSCode, you can compile your python file.