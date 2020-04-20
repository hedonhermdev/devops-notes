# DevOps Notes
  This repo contains minimal configs of various cases of deployment in containers along with other useful tips. Made this as a reference for myself. You probably wont use the exact stack but a lot of niche details are common. 
  
## Miscellaneous Resources:
  - Setting up SSH keys: https://www.digitalocean.com/docs/droplets/how-to/add-ssh-keys/
  
  - Securing Nginx inside a Docker container: https://www.digitalocean.com/community/tutorials/how-to-secure-a-containerized-node-js-application-with-nginx-let-s-encrypt-and-docker-compose

 
## Setting up a Git remote on your private server
  I do this as the root user. Its probably not the best idea but works. 
  
  ```bash
  $ cd ~
  $ mkdir project-name && cd project-name
  $ git init --bare .git
  ```
  Now setup a post-receive hook to checkout to your preffered branch whenever you push to the remote.
  
  ```bash 
  $ cd .git/hooks/
  $ touch post-receive
  $ echo "git git-dir=/root/project-name/.git work-tree=/root/project-name/ checkout deploy -f
  ```
  Replace the values of `git-dir` and `work-tree` with the appropriate directories. Also `deploy` is the name of the branch I use with the production config. 
  
  On your local machine, set up this server as a remote. 
  ```bash
  $ git remote add prodserver ssh://root@<SERVER-IP>:/root/project-name/.git
  $ git checkout -b deploy
  $ git push prodserver deploy
  ```
  
  Also I highly recommend using an ssh-key rather than a root password. 
  
## Homegrown Solution for Continuous Deployment  
This is a post-receive hook I use to deploy whenever I push without having to ssh to the server. 
  ```bash
  #!/bin/bash

  WORK_TREE=/root/marketplace
  COMPOSE_FILE=$WORK_TREE/docker-compose.yml
  KEYCONFIG=$WORK_TREE/marketplace/keyconfig.py

  git --work-tree=$WORK_TREE --git-dir=$WORK_TREE/.git checkout deploy -f

  echo "Hello from prodserver"

  echo "Building docker containers..."

  if [ ! -f $COMPOSE_FILE ]; then
    echo "Compose file not found, are you sure you pushed it?"
    exit 1
  fi


  if [ ! -f $KEYCONFIG ]; then
    echo "Keyconfig file not found, are you sure you pushed it?"
    exit 1
  fi

  docker-compose -f /root/marketplace/docker-compose.yml up --force-recreate --build -d
  RESULT=$?
  if [ $RESULT == 0 ]; then
    echo "Successfully deployed!"
  else
    echo "Failed with exit code $RESULT"
  fi
  ```
  Basically whenever you push to the remote server make sure run a couple of checks and rebuild the existing containers. If you have a test suite too,  just squeeze in a `pytest` command to make sure everything works before bringing down the containers. 
