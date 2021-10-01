# Docker-Update-Script
Create a script to run the 4 docker commands needed to force pull/upgrade docker apps using docker-compose.  Add this to path to make an executable from anywhere command, and automate with CRON.

# Install


### Make a userscript directory and add to PATH

* `mkdir $HOME/bin`

* `PATH=$HOME/bin:$PATH`
  
* `source .profile`

---

### Create the `dup` (docker upgrade) script:

* `nano $HOME/bin/dup`

Copy the script below:

```bash
#!/bin/bash
source $HOME/.profile
CYAN='\033[0;36m'
GREEN='\033[0;32m'
NC='\033[0m' # No Color
echo -e "${CYAN}Change dir to $HOME where your docker-compose.yml file is located${NC}"
cd

echo -e "${CYAN}Stop all containers${NC}"
docker-compose down
echo -e "${GREEN}All Containers have been Stopped${NC}"

echo -e "${CYAN}Pull the latest container release${NC}"
docker-compose pull
echo -e "${GREEN}Finished Pull${NC}"

echo -e "${CYAN}Starting all containers, detatched, and recreating the image using the latest release${NC}"
docker-compose up -d --force-recreate
echo -e "${GREEN}Successfully recreated and started all containers${NC}"

echo -e "${CYAN}Delete the old unused container images${NC}"
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
source $HOME/.profile
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
0 6 * * 6 . $HOME/.profile; dup
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
    `Unattended-Upgrade::Automatic-Reboot "false";`  
  5. Verify that it is working by running the following command:  
    `sudo unattended-upgrades --dry-run`  

# Notes:

* If there are any docker containers that you do NOT want updated, look up the available [TAG's](https://hub.docker.com/r/linuxserver/plex/tags?page=1&ordering=last_updated),  
  edit `docker-compose.yml` and include the specific TAG version after a `:` when defining the image:  
```
nano docker-compose.yml
...
  plex:
    image: linuxserver/plex:1.24.3.5033-757abe6b4-ls76
...
```
