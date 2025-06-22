# Ambari3 Installation Instructions

## Prerequisites

1. This command fetches the list of available packages and their versions from the software repositories you have configured. It's a necessary first step before upgrading:

   ```bash
   sudo apt update
   ```
   
1. This command upgrades all currently installed packages to their latest versions. It won't remove or install new packages:

   ```bash
   sudo apt upgrade
   ```
      
1. if your user is not part of sudoers (solution from stackoverflow):
    1. Open file
       ```bash
       su root 
       nano /etc/sudoers
       ```
   1. Then add the user below admin user like below syntax
       ```bash
       user_name ALL=(ALL)  ALL
       ```
  
1. If you dont have a docker engine installed, check out official manual: [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)

1. if you dont want write `sudo` all the time before `docker` keyword (solution from askubuntu):
    1. Add the docker group if it doesn't already exist:
       ```bash
       sudo groupadd docker
       ```
   1. Add the connected user "$USER" to the docker group. Change the user name to match your preferred user if you do not want to use your current user:
       ```bash
       sudo gpasswd -a $USER docker
       newgrp docker
       ```

## Ambari 

1. Clone this repository first. Alternatively, download the ZIP file and unpack it.  cd into the `ambari3` directory

1. Start the containers (run this from the `ambari3/` directory):

   ```bash
   docker compose up -d
   ```

1. Set up the Ambari repository. The following commands should be run from the `scripts/` directory inside `ambari3/`. This step may take a while (the repository is approximately 7 GB):

   ```bash
   ./setup-ambari-repo.sh
   ```

1. Set up hostnames. We need to correctly configure the hostnames in the hosts file:

   ```bash
   ./setup-hostname.sh
   ```

   If you restart the containers and their IP addresses change, re-run this script to update the hosts file.

1. Run the setup script on each node:

   ```bash
   ./setup.sh
   ```

1. Install the Ambari server on the first node:

   ```bash
   ./install-ambari-server.sh
   ```

1. Install the Ambari agent on all nodes:

   ```bash
   ./install-ambari-agent.sh
   ```

1. Set up the cluster.
   You can now log in to [http://localhost:8080](http://localhost:8080)
   (username: `admin`, password: `admin`) to begin cluster setup.

1. Configuration:

   * When prompted for the base URL, enter: `http://bigtop-hostname0.demo.local`.
     Your OS should be Red Hat 8-based.
   * When prompted for installation nodes, list all nodes:
     `bigtop-hostname0.demo.local bigtop-hostname1.demo.local bigtop-hostname2.demo.local bigtop-hostname3.demo.local`
