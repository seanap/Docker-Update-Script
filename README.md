# Docker-Update-Script
Create a script to run the 4 docker commands needed to force pull/upgrade docker apps using docker-compose.  Add this to path to make an executable from anywhere command.

# Install


### Make a userscript directory and add to PATH

* `mkdir $HOME/bin`

* `PATH=$HOME/bin:$PATH`
  
* `source .profile`

---

### Create the `dup` script:

* `nano $HOME/bin/dup`

Copy the script below:

```bash
#!/bin/bash
cd

docker-compose down

docker-compose pull

docker-compose up -d --force-recreate

docker rmi $(docker images -f "dangling=true" -q) -f
```

Make the script executable:

* `chmod u+x $HOME/bin/dup`

# Use

To update all dockers, type `dup` into the terminal.

