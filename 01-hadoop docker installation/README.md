# Hadoop Docker Installation
## Setting up Google Compute Engine for a Hadoop project
### step 1: Provision a GCP Compute Engine instance for Docker (leave configurations other than those specified as default)
<img src="image\Set-up Google Compute Engine.png" width=100% height=40%>

### step 2: After the VM has been successfully provisioned, set up the network firewall for that VM
<img src="image\VPC_network.png" width=100% height=40%>

### step 3: Set up the Firewall Network according to the configuration above

(Note: The firewall setup described above opens the firewall for all inbound traffic to reduce configuration complexity for beginners in networking. This should be used only for study or demonstration purposes and should not be used in production or for systems handling sensitive data, as it may violate security policies.)

(The main objective is to open TCP port 7180 for Cloudera Manager and TCP port 8888 for Cloudera HUE so they can be accessed via a web browser.)

<img src="image\Set-up Firewall Network.png" width=100% height=40%>

### step 4: After completing the setup, click the SSH button on the Compute Engine page of the created instance to access the SSH terminal

---

## Getting started with Cloudera Hadoop via a Docker container.

### step 1: Install Docker on Ubuntu 18 by following Steps 1–4 from [the DigitalOcean guide](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04)

### step 2: After the installation is complete, use the command `docker pull mikelemikelo/cloudera-spark:latest` to pull the Docker image for use on your VM

### step 3: After successfully pulling the image, you can verify it by using the `docker images` command

### step 4: Run the following command
```sh
docker run --hostname=quickstart.cloudera --privileged=true -it -p 8888:8888 -p
8080:8080 -p 7180:7180 -p 88:88/udp -p 88:88 mikelemikelo/cloudera-spark:latest
/usr/bin/docker-quickstart-light
```
<img src="image\docker run.png" width=100% height=40%>

### step 5: Wait until the container finishes running the Hadoop fundamental commands
<img src="image\the container finishes.png" width=100% height=40%>

### step 6: Use the command `sudo /home/cloudera/cloudera-manager --express && service ntpd start` to start Cloudera Manager
<img src="image\start Cloudera Manager.png" width=100% height=40%>

### step 7: Use the VM’s public (external) IP to access Cloudera Manager via port 7180
For example: http://34.87.136.199:7180/ → username: cloudera, password: cloudera

<img src="image\the VM’s public (external) IP.png" width=100% height=40%>

### step 8: Before starting the cluster, go to the Hive service on the left-hand side and set the configuration values mentioned above to `None`
<img src="image\set the configuration.png" width=100% height=40%>

### step 9: Remove unused and unnecessary services, such as Spark and KV Indexer

(The Spark service in CDH is version 1.x.x, which does not support Spark Structured Streaming. Remove this service and instead use the local Spark already installed in the Docker container.)

<img src="image\Remove unused.png" width=100% height=40%>

### step 10: Add the ‘Flume’ service
<img src="image\Add the Flume.png" width=100% height=40%>

### step 11: Start the cluster and wait until all services reach the status All Up and Running
<img src="image\Start the cluster.png" width=100% height=40%>

### step 12: Log in to Cloudera HUE using the VM’s public (external) IP on port 8888 
For example: http://34.87.136.199:8888/ → username: cloudera, password: cloudera
<img src="image\Hue.png" width=100% height=40%>


---

## Cloning a Git repository to use the materials
### step 1: Press Ctrl + P + Q to detach from the Docker container and return to the main VM terminal.
### step 2: Use the command `git clone https://github.com/nuttatunc/hadoop-r2de-data.git` to clone the repository.
### step 3: Use the command `docker container ls` to view the `container_id` of the container running the Hadoop services.
### step 4: Use the command `docker cp hadoop-r2de-data/data <container_id>:/` to copy the files from the Git repository into the container.
### step 5: Use the command `docker exec -it <container_id> /bin/bash` to re-enter the container. You will then see a folder named data containing the code and other related components.

<img src="image\docker container Is.png" width=100% height=40%>
<img src="image\docker cp.png" width=100% height=40%>
<img src="image\data clone.png" width=100% height=40%>