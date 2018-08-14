# dockerworkshop
Docker workshop
Environment:

Operating system: Centos 7
Docker Package:  docker-1.13.1-68.gitdded712.el7.centos
Two nodes with IP address: 192.168.122.71 and 192.168.122.72

Activities:
You will be performing the following activities on the desktop system (user/password: root/redhat6). Choose either one of the node.

A. Introduce yourself to the basic docker administrative commands
B. Build your own docker container for a sample application.
C. Build your own container registry
D. Docker utilities for troubleshooting
E. Setting resource limits for containers
F.  Docker networking
. 
Fire up the docker

Docker Application Container Engine, should already be up and running. If not, start the service



        
Investigating Docker environment

Run docker with the version and info options to get a feel for your Docker environment.

docker version: The version option shows which versions of different Docker components are installed. 
Example output










docker info: The info option lets you see the locations of different components, such as how many local containers and images there are, as well as information on the size and location of Docker storage areas.









Pull docker container images from an external registry

“docker info” will list all the registries that are configured. By default docker.io/docker hub is enabled. Execute the below command to pull a image from the docker registry




You can also search for container images available in docker-hub. Additional registries can be enabled via the docker configuration file /etc/containers/registries.conf 



Investigating Docker images 

With the images loaded into your local registry, you can use the docker images command to view those images.  Here's how to list the images in your local registry:





Working with Docker containers

The docker run command allows you to choose which command/process that should run in a container. Once a container is running, you can stop, start, and restart it. You can remove containers you no longer need (in fact you probably want to).

Running Docker containers

When you execute the docker run command, you essentially spin up and create a new container from the respective container image. 

The command you pass to the docker run command  only has visibility of the environment inside the container  
 
For example, by default, the running applications sees:
    • The filesystem provided by the container image.
    • A new process table from inside the container (no processes from the host can be seen).
    • New network interfaces (by default, a docker0 bridge provides a private IP address to each containter).
Run the following  command to spin a docker container and observe the outputs. 





Investigating from outside of a Docker container

Open a new terminal and try the following commands.


docker ps: The ps option shows all containers that are currently running:




To list all containers both running and stopped, use `docker ps -a`








docker inspect: To inspect the metadata of an existing container, use the docker inspect command. You can show all metadata or just selected items for the container. For example, to show all metadata for a selected container, type: Find the container id and substitute appropriately



Starting and stopping containers

To start a previously run container that wasn't removed, use the start option. To stop a running container, use the stop option.

Starting containers: A docker container that doesn't need to run interactively can start with only the start option and the container ID or name:

Example




To start a container so you can work with it from the local shell, use the -a (attach) and -i (interactive) options. 



Stopping containers: 
To stop a running container that is not attached to a terminal session, use the stop option and the container ID or number. For example:








Creating a new image from a running container

Install httpd on a new container:  

Start the container first

From within the container, install the httpd service and then do a yum clean all. Yum clean all will remove the cache and reduce the resultant image size. Do not exit from the bash shell




Commit the new image: 

Get the new container's ID or name (docker ps -l), then commit that container to your local repository. 




If this is the container where you have installed httpd, then Commit the container to create a new image. You can add a comment (-m) and the author name (-a), along with a new name for the image (centos_httpd). 


docker images command will list the newly committed image





Start a container using the new image

Using the image you just created, run the following docker run command to start the Web server (httpd) you just installed. For example:


In the example just shown, the Apache Web server (httpd) is listening on port 80 on the container, which is mapped to port 8080 on the host.

Verifying the newly started container

To make sure the httpd server you just launched is available, you can try to get a file from that server. Either open a Web browser from the host to address http://localhost:8080 or use a command-line utility, such as curl, to access the httpd server:




Building an image from a Dockerfile

The procedure here involves creating a Dockerfile file that includes many of the features illustrated earlier:
    • Choosing a base image 
    • Installing the packages needed for an Apache Web server (httpd) 
    • Mapping the server's port (TCP port 80) to a different port on the host (TCP port 8080) 
    • Launching the Web server 
      
Create project directory: 



Create the Dockerfile file: 
Open a file named Dockerfile using any text editor (such as vim Dockerfile) and copy the contents to build a new httpd container image. “From” directive indicates the base image or platform image. You can change it to “fedora” and try




       




Build the image: 
To build the image from the Dockerfile file, you need to use the build option and identify the location of the Dockerfile file (in this case just a "." for the current directory):












Removing Images

To see a list of images that are on your system, run the docker images command. To remove images you no longer need, use the docker rmi command, with the image ID or name as an option. (You must stop any containers using an image before you can remove the image.) Here is an example:


Configuring docker registry
Private docker registry can be installed and configured on the docker host. Container images that are build on a host are usually pushed to the private docker registry, so that the same image can be used on any other host.
Start the docker registry using the below command



Docker registry by default will listen to port 5000. For this demonstration, we are not configuring certificate based access. Add the newly created registry as an insecure-registry in '/etc/containers/registries.conf’ . Example


Replace IP address of the system appropriately and use the same port. Save the file.
Edit the file “/etc/docker-distribution/registry/config.yml“ to make the docker-distribution service to listen on port 1500.  After making the change, then start docker-distribution service. 


Make sure Port 1500 is added to your firewall. Do not flush the existing rules.


Tag and push your image
Find the image id of the newly created container image, tag it and then push it to your local registry


Once you have tagged it, it is ready to be pushed to your registry


Now your container image is available for your peers. Image can be pulled from any other systems. Port 5000 should be enabled in the firewall


By default docker will pull the image that has been tagged as latest. If you have want to pull a particular version, then specify the tag or label in the 'docker pull' command. '-a' option passed with docker pull command will pull all the tags from the registry







Docker utility for troubleshooting

The command 'docker exec' and 'docker logs'  are very useful for troubleshooting issue

docker exec . This allows a user to spawn a process inside their Docker container via the Docker API and CLI.  This will allow you to inspect a container while it is running

If you have an existing  running container named centos_httpd, then execute the below command



This will create a new Bash session inside the container centos_httpd.

So now you can check what is happening inside the container

docker logs 

The docker logs command batch-retrieves whatever logs are present for a container at the time of execution. The option '–follow' is useful as it will retrieve all the logs from the containers stdout and stderr
If you have an existing  running container named centos_httpd, then execute the below command




docker stats

Display a live stream of one or more containers' resource usage statistics.










Configuring resource limits on containers

While starting a container, you can control the system resources that the application container can consume. Along with docker run, you can pass –cpus to limit the number of cpus or –memory to limit the memory

Example for setting memory limits. Start a container using the below command and at the container prompt, execute the dd command as shown






Does the command get killed ?. Try to execute, dd command with modified values. 






While the command is running, execute docker stats and observe the behavior

Docker Networking

