# Jenkins

## Commands to create a container functional

1. Open up a command prompt window and similar to the macOS and Linux instructions above do the following:
   
2. Create a bridge network in Docker
    -user$ docker network create jenkins

3. Run a docker:dind Docker image
    - user$ docker run --name jenkins-docker --detach --privileged --network jenkins --network-alias docker --env DOCKER_TLS_CERTDIR=/certs --volume jenkins-docker-certs:/certs/client --volume jenkins-data:/var/jenkins_home --publish 2376:2376 docker:dind

4. Customise official Jenkins Docker image, by executing below two steps:
    4.1. Create Dockerfile with the following content:
          - `
          FROM jenkins/jenkins:2.361.1-jdk11
          USER root
          RUN apt-get update && apt-get install -y lsb-release
          RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
          https://download.docker.com/linux/debian/gpg
          RUN echo "deb [arch=$(dpkg --print-architecture) \
          signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
          https://download.docker.com/linux/debian \
          $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list
          RUN apt-get update && apt-get install -y docker-ce-cli
          USER jenkins
          RUN jenkins-plugin-cli --plugins "blueocean:1.25.8 docker-workflow:521.v1a_a_dd2073b_2e"
          `
      
    -  Build a new docker image from this Dockerfile and assign the image a meaningful name, e.g. "myjenkins-blueocean:2.361.1-1":
       - user$ docker build -t myjenkins-blueocean:2.361.1-1 .
      > Keep in mind that the process described above will automatically download the official Jenkins Docker image if this hasn’t been done before.
 
    - Run your own myjenkins-blueocean:2.361.1-1 image as a container in Docker using the following docker run command:
        - user$ docker run --name jenkins-blueocean --restart=on-failure --detach --network jenkins --env DOCKER_HOST=tcp://docker:2376 --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 --volume jenkins-data:/var/jenkins_home --volume jenkins-docker-certs:/certs/client:ro --publish 8080:8080 --publish 50000:50000 myjenkins-blueocean:2.361.1-1
        
### Links
- [Docker-Jenkins-onWindows](https://www.jenkins.io/doc/book/installing/docker/#on-windows)
