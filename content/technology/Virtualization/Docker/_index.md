## Containers 
- A container is a run-able instance of an image. You can create, start, stop, move or delete a container using the Docker API call or CLI. You can connect a container to one or more networks and attach storage to it. 
- A container is relatively well isolated from the host and is customization. (sketchy)
- So, what's the difference between a container and a VM?
	- A container use's the host OS and the image is more of an application or executable, not an operating system. 
## Images 
- These images are composed of layers, each representing a set of file changes. These layers are stacked on top of each other to create the final image. Container runtimes use these images to create and run containers. 
- If you're familiar with `chroot`, think of a container as extended version of `chroot`. The filesystem is simply coming from the image. But, a container adds additional isolation not available when simply using chroot.
## Usage 
- `sudo docker image ls` - List loaded images
- `sudo docker image load` - load an image
- `sudo docker run [image name/id]` - start and image
- `sudo docker ps -a` - list running containers 
- `sudo docker exec -it [myContainer] sh` - pop a shell
- `docker run --rm -it <image_name> /bin/sh` - start an interactive shell of the image


# Docker 101
## Setting up the Application
- `docker run -d -p 80:80 dockersamples/101-tutorial`
	-  `-d` - run the container in detached mode (in the background)
	- `-p 80:80` - map port 80 of the host to port 80 in the container
	- `dockersamples/101-tutorial` - the image to use
- Upload application zip files and create the Dockerfile
- `docker build -t <application name> .`
	- This command used the Dockerfile to build a new container image. You might have noticed that a lot of "layers" were downloaded. This is because we instructed the builder that we wanted to start from the `node:10-alpine` image. But, since we didn't have that on our machine, that image needed to be downloaded.
	- After that, we copied in our application and used `yarn` to install our application's dependencies. The `CMD` directive specifies the default command to run when starting a container from this image.
- `docker run -dp 3000:3000 docker-101`
	- Remember the `-d` and `-p` flags? We're running the new container in "detached" mode (in the background) and creating a mapping between the host's port 3000 to the container's port 3000.
## Updating the Application
### Making changes
- After updating a file in the docker image, run `docker build -t <application name> .`
- Use `docker run -dp 3000:3000 docker-101` to start the application again
	**WARN ! :** The old container must be stopped and removed before starting again
### Replacing our Old Container
- Make sure to shut down the original container
- Get the container ID
	- `docker ps`
- Stop the container 
	- `docker stop <the-container-id>`
- Remove the container 
	- `docker rm <the-container-id>`
## Sharing the App 
### Docker push
- Log into docker 
	- `docker login -u YOUR-USER-NAME`
- Set the tag 
	- `docker tag docker-101 YOUR-USER-NAME/101-todo-app`
- Push the app to Docker 
	- `docker push dockersamples/101-todo-app` 
- Run the application 
	- `docker run -dp 3000:3000 YOUR-USER-NAME/101-todo-app`
## Persistent DB
- Start a `ubuntu` container that will create a file named `/data.txt` with a random number between 1 and 10000.
	- `docker run -d ubuntu bash -c "shuf -i 1-10000 -n 1 -o /data.txt && tail -f /dev/null"`
- Validate we can see the output by `exec`'ing into the container. To do so, you need to get the container's ID (use `docker ps` to get it).
	- `docker exec <container-id> cat /data.txt`
### Container Volumes
With the previous experiment, we saw that each container is effectively read-only. While containers can create, update, and delete files, those changes are lost when the container is removed and are isolated to that container. With volumes, we can change all of this.

[Volumes](https://docs.docker.com/storage/volumes/) provide the ability to connect specific filesystem paths of the container back to the host machine. If a directory in the container is mounted, changes in that directory are also seen on the host machine. If we mount that same directory across container restarts, we'd see the same files.

There are two main types of volumes. We will eventually use both, but we will start with **named volumes**.

- Create a volume by using the `docker volume create` command.
	- `docker volume create todo-db`
- Start the todo container, but add the `-v` flag to specify a volume mount. We will use the named volume and mount it to `/etc/todos`, which will capture all files created at the path.
	- ` docker run -dp 3000:3000 -v todo-db:/etc/todos docker-101`
- `docker volume inspect` command.
	- Shows where in memory the volume is stored!
