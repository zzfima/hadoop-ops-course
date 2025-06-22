# Ambari3 Installation Instructions

## Prerequisites

1. **Update package lists**  
   This command fetches the list of available packages and their versions from the configured software repositories. It's a necessary first step before upgrading:
   ```bash
   sudo apt update
   ```

1. **Upgrade installed packages**
   This command upgrades all currently installed packages to their latest versions. It won't remove or install new packages:

   ```bash
   sudo apt upgrade
   ```

1. **Add user to sudoers (if not already)**
   *Solution from [Stack Overflow](https://stackoverflow.com/questions/47806576/username-is-not-in-the-sudoers-file-this-incident-will-be-reported)*:

   1. Open the sudoers file:

      ```bash
      su root 
      nano /etc/sudoers
      ```
   1. Add your user below the admin entry using the following syntax:

      ```bash
      user_name ALL=(ALL) ALL
      ```

1. **Install Docker Engine (if not installed)**
   Refer to the official Docker documentation:
   [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)

1. **Run Docker without `sudo` (optional)**
   *Solution from [Ask Ubuntu](https://askubuntu.com/questions/477551/how-can-i-use-docker-without-sudo)*:

   1. Add the Docker group if it doesn't already exist:

      ```bash
      sudo groupadd docker
      ```
   1. Add the current user (`$USER`) to the Docker group. Replace `$USER` with your actual username if needed:

      ```bash
      sudo gpasswd -a $USER docker
      newgrp docker
      ```

## Ambari Setup

1. **Clone this repository**
   Alternatively, download the ZIP file and unpack it.
   
1. Change into the `ambari3` directory:

   ```bash
   cd ambari3
   ```

1. **Start the containers**

   ```bash
   docker compose up -d
   ```

1. **Check running containers**

   ```bash
   docker compose ps
   ```

1. Change into the `ambari3/scripts` directory:

   ```bash
   cd scripts
   ```
   
1. **Set up the Ambari repository**
   *Note: This step may take a while (the repository is approximately 7 GB).*

   ```bash
   ./setup-ambari-repo.sh
   ```
   you can see its downloaded into directory `ambari3/ambari-repo`

1. **Set up hostnames**
   This script correctly configures hostnames in the hosts file:

   ```bash
   ./setup-hostname.sh
   ```

   *If you restart the containers and their IP addresses change, re-run this script to update the hosts file.*

1. After setup hostname you can check it:

   ```bash
   cat ../conf/hosts
   ```

   you can see 4 ip addresses and their names
   
1. **Run setup on each node**

   ```bash
   ./setup.sh
   ```

1. **Install the Ambari server on the first node**

   ```bash
   ./install-ambari-server.sh
   ```

1. **Cluster Configuration uses Ambari Web UI**
   Go to [http://localhost:8080](http://localhost:8080)
   *Default credentials: `admin` / `admin`*

      1. Click `Launch Install Wizard`
      1. Name it  `demo`. click next
      1. Remove all repositories except `redhat8`
      1. run:
        ```bash
         cat ../conf/hosts
        ```
      1. Copy the first line, which looks like: `bigtop-hostname0.demo.local`
      1. Paste it as `Base URL` of `redhat8`. click next
      1. 

   * When prompted for the **base URL**, enter:
     `http://bigtop-hostname0.demo.local`
     *(Your OS should be based on Red Hat 8)*
   * When prompted for the **installation nodes**, enter:

     ```
     bigtop-hostname0.demo.local 
     bigtop-hostname1.demo.local 
     bigtop-hostname2.demo.local 
     bigtop-hostname3.demo.local
     ```

1. **Install the Ambari agent on all nodes**

   ```bash
   ./install-ambari-agent.sh
   ```

