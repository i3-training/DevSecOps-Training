# Dockerfile

![devops-toys](../images/dockerfile-trivy.png)

## What is Dockerfile ?

Docker can build images automatically by reading the instructions from a Dockerfile. A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image. Dockerfile are another option for creating container images that addresses limitations such image size, and repeatable. Dockerfile are easy to share, version control, reuse, and extend.

Why using Dockerfile

1. Add new runtime libraries, such as database connectors.
2. Include organization-wide customization such as SSL certificates and authentication providers.
3. Add internal libraries to be used by multiple container images for different applications.
4. Trim the container image by removing unused material (such as man pages, or documentation found in /usr/share/doc).
5. Lock either the parent image or some included software package to a specific release to lower risk related to future software updates.

## Build Custom Image With Dockerfile

A Dockerfile is a mechanism to automate the building of container images. Building an image from a Dockerfile is a three-step process.

1. Create a working directory
2. Write the Dockerfile
3. Build the image with Docker

```bash
FROM ubuntu:18.04
LABEL ENV=”DEVELOPMENT”
MAINTAINER maintainer@email.com
RUN apt-get update
RUN apt-get install tomcat
COPY /target/plantuml.jar plantuml.jar
ADD file.tar.xz / .
ADD http://url.com/git.git /usr/local/folder/
ENV DB_NAME=”MySQL”
ENV DB_VERSION=”8.0”
EXPOSE 8080
VOLUME /app/folder
USER user
USER admin
WORKDIR /var/lib/
CMD [“java”, “-jar”, “app.jar”]
ENTRYPOINT [“java”, “-jar”, “app.jar”]
```

1. FROM, It will set the base image of the container.
2. LABEL, It is a key-value pair used to specify metadata information of the image.
3. MAINTAINER, It will give the detail of the author who created this Docker image.
4. RUN, It is used to execute the command on the base image and it will create a new layer.
5. COPY, It is used to copy local files to the container.
6. ADD, It works same as copy but having some more feature like we can extract local tar and add remote URL.
7. ENV, It is used to set environment variables in the key-value pair. These variables are set during the image build and are available after container created.
8. EXPOSE, It will expose the port to access the container. Container will listen on this network port. We can access the output using this port.
9. VOLUME, It will creates a mount point with the specified name.
10. USER, It will sets the user name and user group to use when running the image
11. WORKDIR, It will set the working directory. It will create the directory if not present.
12. CMD, It is used to set a command to execute first when the container starts.
13. ENTRYPOINT, It is used to set the main command for the image. It works as same as CMD instruction. The only difference between CMD and ENTRYPOINT is instructions are not overwritten in ENTRYPOINT.

## Building Images

```bash
#specific building Dockerfile in current directory
docker build -t test-image:0.01 .
```

```bash
#specific building Dockerfile wih path directory
docker build -t test-image:0.01 -f Docker/Dockerfile .
```
