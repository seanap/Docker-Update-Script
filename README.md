# Docker-Update-Script
Create a script to run the 4 docker commands needed to force pull/upgrade docker apps.  Add this to path and make an executable from anywhere command

# Install

Make a userscript directory:
`mkdir $HOME/bin`

Download the `dup` script from `https://github.com/seanap/Docker-Update-Script/blob/main/dup`

Copy the script into `$HOME/bin`

Make the script executable:
`chmod u+x dup`

Add userscript directory to PATH and environment:
`PATH=$HOME/bin:$PATH
sudo nano /etc/environment`
Add `:$HOME/bin` to the PATH line
Save and exit
`source /etc/environment`

#Use

To update all dockers, type `dup` into the terminal.

