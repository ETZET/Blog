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
ssh-key-gen
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