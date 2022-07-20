# Useful Linux and Server Command

## GitHub Related

#### *How to Force Sync with Local Machine*

1. Commit and push your code to your remote repositary
2. On your bluehive, input the following command, these will fetch the codes from GitHub and sync to bluehive
   ```bash
   git fetch -all
   git reset --hard origin/master
   or
   git reset --hard origin/<brance_name> 
   ```
   **IMPORTANT: This will wipe out all unpushed commited local changes, these command are recommned when you edit on your local machine and simply passing the files to bluehive to run**


## Bash Related

#### *How to configure Bash Terminal startup command*

```bash
vim ~/.bashrc
```
Then insert the command you wish to automactically execute when you powerup a terminal window. For example,
```bash
module load python3.9
source venv/bin/activate
```

## SSH Related

#### *How to configure passwordless connection to remote server*
```bash
# change your directory to system ssh directory
cd ~/.ssh
# generate public key
ssh-keygen
```
You will receive prompt to enter name to the public key generated and enter a paraphase for the public key. For simplicity I recommend enter a meaningful key file name, and leave the paraphase blank.

Then, you will find a file `<key_file_name>.pub`, e.g. `example.pub` that contains your public key generated under the current directory, and another file with the same name but without `.pub` that contains your private key. Next, pass the public key to the remote server:

```bash
# submit your public key to remote server
ssh-copy-id -i <key_file_name>.pub <user>@<server>
# for example
ssh-copy-id -i example.pub enting@rochester.meliora.edu
```

Add your private key to the system.
``` bash
ssh-add example
```

#### *Don't want to type the server address everytime?*

You can give a nick name to the server by the following:

With any text editor, open `~/.ssh/config`, create the file if it does not exist. Then input the following to the config file:

```bash
Host <Nickname>
   HostName <server address>
   User <User Name>
   IdentityFile <Path to the public key>
```

Voila! Try ssh to your remote server with the nickname

``` bash
ssh <nickname>
```

#### *How to run jobs that will continue after SSH session shutdown*

Lets say if you have a job that will run for three days. You run the job on the ssh session like you always did, and an hour later, your internet connection was cut off and the ssh tunnel dies. What happens? Your job dies with the ssh tunnel. Here is how to prevent this. 

```bash
nohup long-running-command >output.txt &
```

`nohup` tells the remote server not to kill your process when you exit the terminal/shell, that is when you cut off the SSH session.

`>output.txt` redirect your program's output to a file named `output.txt`.

`&` puts your job into background mode, so you may use the terminal window for other purposes.

**Note for Python Users**

Use the following command to avoid output buffering.

```bash
nohup python -u <python_script> >output.txt &
```

##Python Environment Related

#### *Install Package by Pip when You Are not System Admin*

```bash
pip install --user ...
```

The package will be installed in your user directory rather than the system directory. This will solves the problem is you just need one or two package that is not preinstalled on the server. But what if you want to project that has a complicated package dependency or a version conflict with preinstalled packages? 

#### *How to build python virtual environment*

Use the following command to create a virtual enviornment call `venv`

```bash
python<version> -m venv venv
# Note: it is imperative to specify the python version you wish to use in your virtual environment inside <version>
# For example,
python3.9 -m venv venv
```

You will find a folder named `venv ` under your current working directory

Use below command to activate the virtual enviornment,

```bash
source venv/bin/activate
```
Once you activate the virtual environment, you may install your custom package into it using pip
```bash
python3.9 -m pip install <package-name>
```
When you are done working iwth this virtual environemnt, deactivate by,
```bash
deactivate
```

####*Why bother with a virtual enviornment?*

Virtual Enviornment gives you an isolated workspace and make reproducing working environement convienent. Lets say you are working with a Python project now that has dependency to multiple packages, for example Numpy and Pandas. Then you finshed it, sealed it, and store it on GitHub. Then three years into the future, someone else wanted to use your code. So, they download it, run it, and guess what, their screen is flushed with error message about your program trying to use a numpy function that does not exist. How does that happen? It may happen because the function's name is changed in future version of numpy or the function is 'deceprecated', meaning new version of numpy is no longer providing that function. How do you avoid problem like these? It is to ship your enviornment specification along with your code. They are typically packaged in a `requirements.txt`

To generate your enviornment's specification,
```bash
python -m pip freeze > requirements.txt
```
A sample requirements.txt will look like,
```
numpy==1.23.0
packaging==21.3
pandas==1.4.3
torch==1.11.0
torchaudio==0.11.0
torchvision==0.12.0
```
When you want to reproduce the enviornment, you may convinient configure it run this command inside you virtual enviornement,
```bash
python3.9 -m pip install -r requirements.txt
```