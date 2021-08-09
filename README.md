# Cloud Migration. Kubernetes

**Kubernetes** is an open-source system for automating deployment, scaling, and management of containerized applications. Every year, thousands of companies move their services to cloud. Kubernetes is one of the most popular cloud solutions today. 

This document is a walkthrough on how to deploy an application to Kubernetes. As an example, we will take the ExampleApp application that will listen on port 8800.

## Deploying application to Kubernetes
**Prerequisites**: Docker. This tutorial requires usage of Docker, so make sure you have it installed on your machine. [Here](https://docs.docker.com/desktop/) you can find an information on how to install the latest version of Docker for your OS.

Deploying application to Kubernetes takes few steps:
* Creating an image of an application, 
* Setting up a production infrastructure, and 
* Deploying the image in Kubernetes. 

In this section we’ll focus on Step 1 - Creating an image of an application. 

### Creating an image of an application
**1.** We will start by creating our project directory. Type the following in the terminal:
~~~~
mkdir quickstart_docker
mkdir quickstart_docker/application
mkdir quickstart_docker/docker
mkdir quickstart_docker/docker/application
~~~~

Where:

`quickstart_docker/` - The main directory of our project,

├── `application/`    - Folder that will contain the application’s code,

├── `docker/`         - Folder that will contain all Docker files,

│    ├── `application/`  - Folder that will contain the Dockerfile for the application.

:warning: It is important to keep project directory structured and put the application files and Docker files in their respective folders.

**2.**	Now, let’s set our application folder. Add to `quickstart_docker/application` folder the following application.py file:
~~~~
import http.server
import socketserver

PORT = 8000

Handler = http.server.SimpleHTTPRequestHandler

httpd = socketserver.TCPServer(("", PORT), Handler)

print("serving at port", PORT)
httpd.serve_forever()
~~~~

**3.**	Our application needs an environment to run in (in this case it's Python, and Python needs an operating system with all its dependencies). All of that is available in Dockerhub, so we will just take it from there.

**4.**	Now, let’s add Docker files. Add to `quickstart_docker/docker/application` folder the Dockerfile with the following content:
~~~~
# Use base image from the registry
FROM python:3.5

# Set the working directory to /app
WORKDIR /app

# Copy the 'application' directory contents into the container at /app
COPY ./application /app

# Make port 8000 available to the world outside this container
EXPOSE 8000

# Execute 'python /app/application.py' when container launches
CMD ["python", "/app/application.py"]
~~~~

:warning: Although the Dockerfile is a file in a text form, make sure it doesn’t have the `.txt` extension (it could negatively affect the build). If your Dockerfile has `.txt` extension, just resave it as _*All files_, so it has no extension at all.

**5.**	Now that we have set up our project directory and prepared all the files, it is time to build the application image. Run the following command in the terminal:
~~~~
docker build . -f-docker/application/Dockerfile -t exampleapp
~~~~
Where:

`.` – is a working directory, build context;

`-f docker/application/Dockerfile` – is a docker-file;

`-t exampleapp` – tagging the application image, so we can find it later.

If you'd like to dig deeper into building Docker images, you can find more information [here](https://docs.docker.com/engine/reference/builder/).

**6.**	Our application image is now ready. Let’s verify the successful build of the ExampleApp image by typing the following command in the terminal:
~~~~
$ docker images
~~~~~
And this is the output you're going to get.
~~~~
REPOSITORY             TAG             IMAGE ID            CREATED             SIZE
exampleapp             latest          83wse0edc28a        2 seconds ago       153MB
python                 3.6             05sob8636w3f        6 weeks ago         153MB
~~~~

As you can see, we have successfully created an image of our application using Docker. 

Next, we will be uploading the application image to the repository.
