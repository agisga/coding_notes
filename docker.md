# Docker

<https://docs.docker.com/engine/docker-overview/#the-docker-platform>:

* Develop your application and its supporting components using containers.
* The container becomes the unit for distributing and testing your application.
* When you’re ready, deploy your application into your production environment, as a container or an orchestrated service. This works the same whether your production environment is a local data center, a cloud provider, or a hybrid of the two.

## Basic info

```
## List Docker CLI commands
docker
docker container --help

## Display Docker version and info
docker --version
docker version
docker info

## Execute Docker image
docker run hello-world

## List Docker images
docker image ls

## List Docker containers (running, all, all in quiet mode)
docker container ls
docker container ls --all
docker container ls -aq
```

* Docs: <https://docs.docker.com/>
* Get started: <https://docs.docker.com/get-started/>

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

### Creating an image and running a container

A Dockerfile defines what goes on in the environment inside your container. Access to resources like networking interfaces and disk drives is virtualized inside this environment, which is isolated from the rest of your system. E.g., it can be included alongside a (web) app's code.

1. Create an empty directory, and create `Dockerfile` inside of it:

    ```
    # reference: https://hub.docker.com/_/ubuntu/
    FROM ubuntu:16.04

    # Adds metadata to the image as a key value pair example LABEL version="1.0"
    LABEL maintainer="Alexej Gossmann <www.github.com/agisga>"

    ##Set environment variables
    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8

    RUN apt-get update --fix-missing && apt-get install -y wget bzip2 ca-certificates \
        build-essential \
        tmux \
        curl \
        git-core \
        htop \
        pkg-config \
        python3-dev \
        python3-pip \
        python-setuptools \
        python-virtualenv \
        unzip \
        && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

    RUN echo 'export PATH=/opt/conda/bin:$PATH' > /etc/profile.d/conda.sh && \
        wget --quiet https://repo.continuum.io/archive/Anaconda3-5.1.0-Linux-x86_64.sh -O ~/anaconda.sh && \
        /bin/bash ~/anaconda.sh -b -p /opt/conda && \
        rm ~/anaconda.sh

    ENV PATH /opt/conda/bin:$PATH

    RUN pip3 --no-cache-dir install --upgrade \
            sklearn-pandas

    # Open Ports for Jupyter
    EXPOSE 5000

    #Setup File System
    RUN mkdir ds
    ENV HOME=/ds
    ENV SHELL=/bin/bash
    # The VOLUME statements allow to mount externally mounted volumes.
    # Here specified is the name of the folder within the docker container,
    # which is shared with the host container. The host directory is
    # declared at run time.
    VOLUME /ds
    WORKDIR /ds
    # ADD allows to copy files from the host computer into the docker
    # container when the docker container is run.
    ADD run_jupyter.sh /ds/run_jupyter.sh
    RUN chmod +x /ds/run_jupyter.sh

    # The main purpose of a CMD is to provide defaults for an
    # executing container. Docker containers shutdown if nothing
    # is running - you can run jupyter notebook to keep the container up,
    # or alternatively just run the bash shell (`CMD ["/bin/bash"]`).
    CMD  ["./run_jupyter.sh"]
    ```

    The above `Dockerfile` is based on <https://github.com/hamelsmu/Docker_Tutorial/blob/master/basic_tutorial/Dockerfile>.

2. The `Dockerfile` may refer to some external files. Create them in the same directory. `run_jupyter.sh`:

    ```
    #! /bin/bash

    jupyter notebook --no-browser --allow-root --port=5000 --NotebookApp.token='datascience'
    ```

3. Build the Docker image:

    ```
    ## build an image
    ## -t: name/tag the image
    docker build -t datascience .

    ## check the built image
    docker image ls
    ```

4. Create and run a container from the Docker image.
Run the app (mapping your machine’s port 4000 to the container’s published port 80):

    ```
    docker run -it --name CS_231n --net=host -v ~/github/my_CS231n/:/ds datascience

    ## -it -- run interactively
    ## -p 4000:80 -- mapping your machine’s port 4000 to the container’s published port 80
    ## -d -- detached mode (run in the background)

    ## check running status, and see the abbreviated container ID
    docker container ls

    ## stop the background process (if running in detached mode)
    docker container stop <container_ID>
    ```

    Contrary to the docker message, the web app is served at `http://localhost:4000`.

5. The jupyter notebook (or web app, or whatever specified in `Dockerfile`) is accessible from `http://localhost:5000/` with password `datascience`.

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

    If you plan to push to DockerHub, the use:

    ```
    docker commit <container_name> <username>/<new_image_name>:<tag_name>
    ```

### Pushing images to DockerHub

TODO.
