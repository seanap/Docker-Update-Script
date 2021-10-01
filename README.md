# Docker-Update-Script
Create a script to run the 4 docker commands needed to force pull/upgrade docker apps using docker-compose.  Add this to path to make an executable from anywhere command, and automate with CRON.

# Install


### Make a userscript directory and add to PATH

* `mkdir $HOME/bin`

* `nano $HOME/.bashrc`
* Copy and paste at the end of the file:  
```
if [ -d "$HOME/bin" ] ; then
    PATH="$HOME/bin:$PATH"
fi
```
* Initialize changes:  
  `. ~/.bashrc`

---

### Create the `dup` (docker upgrade) script:

* `nano $HOME/bin/dup`

Copy the script below:

```bash
#!/bin/bash
source $HOME/.bashrc

# Help File
Help()
{
   # Display Help
   echo "Down, Pull, Up, rmi"
   echo
   echo "Syntax: dup [-h|e|o]"
   echo "options:"
   echo "h     Print this Help."
   echo
   echo "e     Excludes specific service"
   echo "      Example: dup -e plex #Excludes plex from shutdown and new pull"
   echo
   echo "o     Only runs on specifid server"
   echo "      Example: dup -o plex #Only updates the Plex image"
   echo
}

# Set Variables
service=''
CYAN='\033[0;36m'
GREEN='\033[0;32m'
NC='\033[0m' # No Color

# Get the options
while getopts ":he:o:" option; do
   case $option in
      h) # display Help
         Help
         exit;;
      e) service="--scale ${OPTARG}=0";;
      o) service=${OPTARG};;
     \?) # incorrect option
         echo "Error: Invalid option"
         exit;;
   esac
done

echo -e "${CYAN}Change dir to $HOME where your docker-compose.yml file is located${NC}"
cd

if [[ $service ]]
then
  echo -e "${CYAN}Stopping containers...${NC}"
  docker-compose down $service
else
  echo -e "${CYAN}Stopping all containers...${NC}"
  docker-compose down
fi
echo -e "${GREEN}All Containers have been Stopped${NC}"

if [[ $service ]]
then
  echo -e "${CYAN}Pulling containers...${NC}"
  docker-compose pull $service
else
  echo -e "${CYAN}Pulling all containers...${NC}"
  docker-compose pull
fi
echo -e "${GREEN}Finished Pull${NC}"

if [[ $service ]]
then
  echo -e "${CYAN}Starting containers...${NC}"
  docker-compose up -d --force-recreate $service
else
  echo -e "${CYAN}Starting all containers...${NC}"
  docker-compose up -d --force-recreate
fi
echo -e "${GREEN}Successfully recreated and started all containers${NC}"

echo -e "${CYAN}Deleting the old unused container images...${NC}"
docker images -q -f dangling=true | xargs --no-run-if-empty --delim='\n' docker rmi
echo -e "${GREEN}Successfully cleaned up${NC}"
echo -e "${GREEN}FINISHED${NC}"
```

Make the script executable:

* `chmod u+x $HOME/bin/dup`

---

### Create the `aup` (apt-get update/upgrade) script:

* `nano $HOME/bin/aup`

Copy the script below:

```bash
#!/bin/bash
source $HOME/.bashrc
CYAN='\033[0;36m'
GREEN='\033[0;32m'
NC='\033[0m' # No Color

echo -e "${CYAN}apt-get update${NC}"
sudo apt-get update

echo -e "${CYAN}apt-get upgrade${NC}"
sudo apt-get upgrade -y

echo -e "${CYAN}apt-get autoremove${NC}"
sudo apt autoremove -y
echo -e "${GREEN}FINISHED${NC}"
```

Make the script executable:

* `chmod u+x $HOME/bin/aup`

---

# Use

### Manual

* To update/upgrade Ubuntu, type `aup` into the terminal
* To update/upgrade all docker containers, type `dup` into the terminal.

### Automated

* `crontab -e`
* Add the two lines below, in my example it runs `dup` once a week, 6am on sat:
```
shell=/bin/bash
0 6 * * 6 . $HOME/.bashrc; dup
```
* Best not to automate aup, if you want automated security upgrades use a package called `unattended-upgrades`
  1. Update the server, run:  
    `aup`  
  2. Install unattended upgrades on Ubuntu:  
    `sudo apt install unattended-upgrades apt-listchanges bsd-mailx`  
  3. Turn on unattended security updates, run:  
    `sudo dpkg-reconfigure -plow unattended-upgrades`  
  4. Configure automatic updates, enter:  
    `sudo nano /etc/apt/apt.conf.d/50unattended-upgrades`  
    Uncomment or add the following:  
    `Unattended-Upgrade::Automatic-Reboot "true";`  
    `Unattended-Upgrade::Automatic-Reboot-WithUsers "true";`  
    `Unattended-Upgrade::Automatic-Reboot-Time "04:00";`  
  5. Verify that it is working by running the following command:  
    `sudo unattended-upgrades --dry-run --debug`  

# Notes:

* If there are any docker containers that you do NOT want updated, look up the available [TAG's](https://hub.docker.com/r/linuxserver/plex/tags?page=1&ordering=last_updated),  
  edit `docker-compose.yml` and include the specific TAG version after a `:` when defining the image.  
  Example below locks plex to version 1.24.3:
```
nano docker-compose.yml
...
  plex:
    image: linuxserver/plex:1.24.3.5033-757abe6b4-ls76
...
```
