# Jenkins Build Images

This repository is used for Dockerfiles that create Jenkins base build images.

## Introduction

Future Airlines use microservices (docker) in order to execute builds.  The advantages of this approach are:

1. By using ECS to run the build images, we can easily scale build capacity up and down by scaling the size of the ECS cluster.
2. By using tagged Docker images we can easily run different versions of the same software for building.  E.g. we could have one image
   for java 7 builds and another image for java 8 builds.
3. The containers are destroyed once the build is complete meaning that builds clean up after themselves.

This repository contains a Dockerfile for each type of build image.  For example, there is a Dockerfile that builds an image with Openjdk-8
installed.  This image can then be used to run java apps.

The benefit of this approach is that you don't have to worry about using Docker commands within your Jenkinsfile build.  You just reference the
appropriate image and then it will be spun up and executed in Amazon ECS for you.  See 'How do I use my image?' for more information.

## ECS Build Slaves

All of these images are used by Jenkins to perform builds in Amazon's ECS service.  We accomplish this using the Jenkins ECS plugin
(https://wiki.jenkins.io/display/JENKINS/Amazon+EC2+Container+Service+Plugin).

Every Dockerfile is built in to an image and stored in the same ECR repository.  We distinguish between images using Docker tags.  For example:

1. future-airlines-jenkins-base-images/openjdk7  # An image with an OpenJDK 7 environment
2. future-airlines-jenkins-base-images/openjdk8  # An image with an OpenJDK 8 environment

## How do I build my image?

You probably want to start by changing your working directory and user in your Dockerfile to /opt and root.

```
USER root
WORKDIR /opt
```

Once you've done that you can use normal Docker commands to install packages that you think you need in the image.

Finally, change your working directory and user back to original values.

```
USER jenkins
WORKDIR /opt/build
```

## How do I test my image?

It's important to check that your Dockerfile builds before you create your PR. It is possible to auth to AWS so you can download the base image from ECR
but if you want to test quickly then just replace the FROM line with `FROM jenkins/jnlp-slave:latest`.  Don't forget to change it back before you push
your PR.

## How do I use my image?

So, once you've created your Dockerfile you are going to want to build and use it.

It is built via a Jenkins job.  The Jenkins job will ask you to select from a list of images that
is based on the sub-directories under 'images'.  The image will then be built and tagged with the
same name as the sub-directory.

If it's a new image being built for the first time you will need to engage the TechOps team to add
the new image as a node type in Jenkins.  The node type name will match the docker tag.

Now you can reference this node name in your Jenkinsfile.  Just add the name in brackets after the
node keyword.

```
# Jenkinsfile
node("node-10") { // node-10 docker tag
    withMaven(mavenSettingsConfig:'my-maven-settings') {
       sh "mvn clean deploy"
    }
}
```
