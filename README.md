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

#Change dir to $HOME where your docker-compose.yml file is located
cd

#Stop all containers
docker-compose down

#Pull the latest container release
docker-compose pull

#Start all containers, detatched, and recreate the image with the latest release
docker-compose up -d --force-recreate

#Delete the old unused container images
docker images -q -f dangling=true | xargs --no-run-if-empty --delim='\n' docker rmi
```

Make the script executable:

* `chmod u+x $HOME/bin/dup`

---

### Create the `aup` (apt-get update/upgrade) script:

* `nano $HOME/bin/aup`

Copy the script below:

```bash
#!/bin/bash
sudo apt-get update
sudo apt-get upgrade -y
```

Make the script executable:

* `chmod u+x $HOME/bin/aup`

---

# Use

To update/upgrade Ubuntu, type `aup` into the terminal

To update/upgrade all docker containers, type `dup` into the terminal.

