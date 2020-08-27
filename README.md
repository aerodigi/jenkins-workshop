# An Introduction to Jenkins & Continuous Integration

Just a little something to introduce some people to CI/CD with Jenkins

## Requirements

You will need [Docker](https://docs.docker.com/get-docker/) and [Docker Compose](https://docs.docker.com/compose/install/), check you have both with this command...

    docker -v && docker-compose -v 

## Preparation

Fork https://github.com/WillGibson/jenkins-workshop to your GitHub account, clone your fork to your machine and create starting point...

    git clone git@github.com:<your_github_username>/jenkins-workshop.git \
    && cd jenkins-workshop \
    && git revert HEAD --no-edit \
    && git push
    
Not that the above assumes that the person running the workshop left the repos such that the second to last commit has the Jenkinsfile full of ToDos like in https://github.com/WillGibson/jenkins-workshop/commit/0ebcc6a4c9687d53840e94fb7b13a1b941eb1540#diff-58231b16fdee45a03a4ee3cf94a9f2c3

## Sample App

A hacked together Flask API in a docker container

Build: `./build.sh`

Run: `./run.sh`

Test: `./dockertest.sh`

## Jenkins

### Setting up Jenkins

* Move into the `jenkins` folder `cd jenkins`

* Fire up Jenkins in a Docker container with Docker Compose `docker-compose up`

* Hit http://localhost:8080/ and log in with the admin password you got in the output from the previous command

* Install suggested plugins

* Create your admin user and click through past "Save and continue"

### Setting up our pipeline

* Click "Create a job"

* Enter a name, select "Pipeline" and click "OK"

* General

  * Check "Do not allow concurrent builds"

  * Check "GitHub project" and enter the URL "https://github.com/<your_github_username>/jenkins-workshop"
  
  * Check "This project is parameterised"
  
    * Select "String parameter"
    * Enter "BRANCH" for the name
    * Enter "master" as the default

* Build Triggers

  * Ignore this for now
  
* Advanced Project Options

  * Ignore this for now

* Pipeline

  * Select "Pipeline script from SCM" (Source Code Management)
  
  * Enter the repository URL "https://github.com/<your_github_username>/jenkins-workshop"
  
  * Delete the "Branch Specifier" (so it can build any branch based on the branch parameter)
  
  * Uncheck "Lightweight checkout"

* Click "Save"

## Deploying to Heroku

* (Create a Heroku account](https://signup.heroku.com/login)

* Create a Heroku app

* Install the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli#download-and-install)

* Set the stack of your app to container `heroku stack:set container <your_heroku_app_name>`

* Create a `heroku-deploy` branch from master and push it up

* Connect the Heroku app to your Git repository

* Set the Heroku app to deploy when changes are pushed to the `heroku-deploy` branch

* Create a deploy key

  * `cd .ssh`
  * `ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`
  * `cd ../`
  
* Add the deploy key to you GitHub repo with write access

* Now when you build the master branch it should deploy to Heroku

### Useful commands

* Log into the jenkins container `docker exec -it jenkins_workshop /bin/bash`

* Run the Jenkins container in the background `docker-compose up -d`

* Stop the Jenkins container if you ran it in the background `docker-compose down`

### References

* https://github.com/jenkinsci/docker/blob/master/README.md

* https://www.jenkins.io/doc/book/pipeline/jenkinsfile/

* https://itnext.io/docker-in-docker-521958d34efd

* https://www.cloudbees.com/blog/getting-started-blue-ocean

* https://devcenter.heroku.com/articles/build-docker-images-heroku-yml

## Loose notes for the person running the workshop

* Introduce yourself and let the others introduce themselves
* Make sure everyone has Docker and Docker Compose
* Make sure everyone can read the stuff on your screen share
* Explain what CI/CD is
* Tell them what Jenkins is
* Get everyone's local Dockerised Jenkins fired up and get everyone logged in
* Talk about agents (just what they are and that we're not bothering for this exercise)
* Set up pipeline in the Jenkins UI
* Talk about GitHub triggers (probably the normal way, but we can't do because it here because this Jenkins is not being exposed to the web)
* Add Build & Test stages in a branch
* Make the test fail to illustrate the build stopping if something goes wrong
* Merge the branch into master and build
* Add the when condition to Deploy stages and onward in a branch so that it only deploys etc. on master
* Merge the branch into master and build to demonstrate it now doing those steps again
* Deploy to Heroku
* Smoke Tests (ToDo)
* Rollback on Smoke Test Failure (ToDo)
* Show them Blue Ocean (much more friendly UI)
