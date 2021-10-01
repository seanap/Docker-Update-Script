# Docker-Update-Script
Create a script to run the 4 docker commands needed to force pull/upgrade docker apps using docker-compose.  Add this to path to make an executable from anywhere command.

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
* Add the three lines below, in my example it runs `aup` everyother day at 4am, and `dup` once a week on sat morning:
```
shell=/bin/bash
0 4 * * 1/2 aup
0 6 * * 6 dup
```

