# Docker

<https://docs.docker.com/engine/docker-overview/#the-docker-platform>:

* Develop your application and its supporting components using containers.
* The container becomes the unit for distributing and testing your application.
* When you’re ready, deploy your application into your production environment, as a container or an orchestrated service. This works the same whether your production environment is a local data center, a cloud provider, or a hybrid of the two.

### Resources

* Docs: <https://docs.docker.com/>
* Get started: <https://docs.docker.com/get-started/>
* [A good presentation on Youtube](https://www.youtube.com/watch?v=oO8n3y23b6M), and the [associated source files](https://github.com/alysivji/talks/tree/master/data-science-workflows-using-docker-containers)

## Basic info

```
## List Docker CLI commands
docker
docker container --help

## List Docker images
docker image ls

## List Docker containers (running, all, all in quiet mode)
docker container ls
docker container ls --all
docker container ls -aq
```


## Containers

```
docker build -t friendlyhello .  # Create image using this directory's Dockerfile
docker run -p 4000:80 friendlyhello  # Run "friendlyname" mapping port 4000 to 80
docker run -d -p 4000:80 friendlyhello         # Same thing, but in detached mode
docker container ls                                # List all running containers
docker container ls -a             # List all containers, even those not running
docker container stop <hash>           # Gracefully stop the specified container
docker container kill <hash>         # Force shutdown of the specified container
docker container rm <hash>        # Remove specified container from this machine
docker container rm $(docker container ls -a -q)         # Remove all containers
docker image ls -a                             # List all images on this machine
docker image rm <image id>            # Remove specified image from this machine
docker image rm $(docker image ls -a -q)   # Remove all images from this machine
docker login             # Log in this CLI session using your Docker credentials
docker tag <image> username/repository:tag  # Tag <image> for upload to registry
docker push username/repository:tag            # Upload tagged image to registry
docker run username/repository:tag                   # Run image from a registry
```

### Permissions

To avoid permission errors of all sorts, add your user to the `docker` group.

### Example: A simple Miniconda based Docker container

#### Creating an image and running a container

A Dockerfile defines what goes on in the environment inside your container. Access to resources like networking interfaces and disk drives is virtualized inside this environment, which is isolated from the rest of your system. E.g., it can be included alongside a (web) app's code.

1. Create an empty directory, and create `Dockerfile` inside of it:

    ```
    # Use latest miniconda image as parent
    # miniconda is python + conda installer
    FROM continuumio/miniconda3

    # Meta-data
    LABEL maintainer="Alexej Gossmann <www.github.com/agisga>" \
          description="Docker Data Science Project\
          Libraries inside image. Data/code mounted via shared folder.\
          Easy to set up a new developmenet environment."

    # Set the working directory to /app
    WORKDIR /app

    # Install a few libraries
    RUN conda install jupyter -y && \
        conda install numpy -y && \
        conda install pandas -y && \
        conda install scikit-learn && \
        conda install scikit-image && \
        conda clean -y -all

    # Make port 8888 available to the world outside this container
    EXPOSE 8888

    # Create mountpoint
    VOLUME /app/data

    # Run jupyter when container launches
    CMD ["jupyter", "notebook", "--ip='*'", "--port=8888", "--no-browser", "--allow-root"]
    ```

    The above `Dockerfile` is based on <https://github.com/alysivji/talks/blob/master/data-science-workflows-using-docker-containers/workflow2-data-science-project/Dockerfile>.
    The `Dockerfile` may refer to some external files, which need to be in the specified host directory (usually the same directory which holds the Dockerfile).

2. Build the Docker image:

    ```
    docker build -t <imagename> .
    ## `.` for "current directory"

    ## check the built image
    docker image ls
    ```

3. Create and run a container from the Docker image. Run the app (mapping your machine’s port 9999 to the container’s published port 8888):

    ```
    docker run -p 9999:8888 --name <containername> -v ~/dir/to/be/mounted/as/volume/inside/container/:/app/data <imagename>
    ## `/app/data` is the destination directory (mount point) inside the container; it was also specified in the Dockerfile above.

    ## Other options:
    ## -it -- run interactively
    ## -d -- detached mode (run in the background)

    ## check running status, and see the abbreviated container ID
    docker container ls

    ## stop the background process (if running in detached mode)
    docker container stop <container_ID>
    ```

    The container will shut down as soon as it is not running a process anymore (or when manually stopped). To prevent shutdown, something like a bash shell or a jupyter notebook has to be running. Contrary to the docker message, the jupyter notebook (or web app, or whatever specified in `Dockerfile`) is served at `http://localhost:9999`.

4. To restart the container after it has shut down:

    ```
    docker start -ia <containername>
    ## -a attaches to STDIN/STDOUT/STDERR
    ```

### Interacting with the container

* Attach a new terminal session to the container:

    ```
    docker exec -it container1 bash
    ```

    This specifies the container name (`container1`), and the application we want to run (`bash`).

* Save the state of the container as a new image:

    ```
    docker commit <container_name> new_image_name:tag_name(optional)
    ```

    If you plan to push to DockerHub, then use:

    ```
    docker commit <container_name> <username>/<new_image_name>:<tag_name>
    ```

### Pushing images to DockerHub

TODO.
