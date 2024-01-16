# gitlab-cicd

## Create a Gitlab repo

To create a new repo in Gitlab

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/5a7e94a1-0086-4c93-aea1-af0e655c9c1a)

New project -> Create a blank project

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/7880ca54-4591-4197-a60e-b2501c444630)

You can clone this repo and share this files to Gitlab repo using below commands

```
https://github.com/kohlidevops/candycrush.git
```

commands to push code

```
cd mynodeapp-gitlab-cicd
git init --initial-branch=main
git remote add origin https://gitlab.com/kohlidevops/mynodepp.git
git add .
git commit -m "Initial commit"
git push --set-upstream origin main
```

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/64c68d28-f204-4721-8b16-5f773f0710cb)

## Launch EC2 Instance

To launch ubuntu22 EC2 instance with t3.medium and SSH to machine then install docker using below commands

```
sudo apt-get install docker.io -y
sudo usermod -aG docker $USER
newgrp docker
sudo chmod 777 /var/run/docker.sock
```

### Install Sonarqube

To setup Sonarqube using docker container

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/400e3077-5767-484d-b7da-81cf9ffb40fc)

Default credentials for sonarqube

```
login url - ec2-publicip:9000
username - admin
password - admin
```

To update the new password and go to dashboard

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/5ac70624-2763-405e-ba56-e6ea49442aab)

## To create gitlab-ci yaml file

To create a .gitlab-ci.yml with below code

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/73978570-7da2-4512-a631-ccfc1392be49)

```
stages:
    - npm
Install dependecy:
    stage: npm
    image:
        name: node:16
    script:
        - npm install
```

1. stages: Defines the different stages of the CI/CD pipeline. In this case, there is a single stage named "npm."
2. Install dependency job:
    stage: npm: Specifies that this job belongs to the "npm" stage.

   image: Specifies the Docker image to be used for the job. In this case, it's using the official Node.js 16 image.

   script: Contains the commands to be executed as part of the job. It simply runs npm install to install project dependencies.

Commit the changes and it will automatically start the build

Now click on Build and Pipelines

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/895f19c4-8c7c-4eca-a9fc-30b8b659f35e)

You can see the build output

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/996d6a34-92e1-4cb3-a978-76c5037b9856)

My job has been succeeded.

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/ae7120aa-bb20-45cf-b85a-445c916c51ee)


### Add Sonarqube stage

Now add the Sonarqube stage to the pipeline

Go to the Sonarqube dashboard and click on Manually.

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/f4fd1def-f73b-4094-bd95-c5bd51fc8c55)

Provide the name of the Project and click on Setup

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/f682759e-48db-4d1f-af5e-48702bae4c9a)

Select the CI tool as GitLab CI

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/8839eed6-b35e-4629-a650-848596cfe102)

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/85e2dba8-f2d3-42ec-b282-5503ac826f53)

Go to Gitlab and click on + and Newfile

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/4b6fb6ac-2652-4183-b5c8-69cb46f08903)

file name called sonar-project.properties and add below code (This contents are available in Sonarqube)

```
sonar.projectKey=candycrush
sonar.qualitygate.wait=true
```

commit the file to create a file.

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/77828260-855e-464d-b65a-5c4fda761fa5)

### To generate the Sonarqube token

To generate the token in Sonaqube and add it to the Gitlab -> Your project repo -> Settings -> CICD -> Variables -> Expand -> Add variables

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/bea85f28-074a-4f59-9e17-7bfab7a778f2)

```
key - SONAR_TOKEN
Value - aaaaaaabbbbbbbbbbccccccccccdddddddd
```

To add one more variable for Sonarqube URL

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/cab5910e-38c4-42fc-85f3-19c488249b5c)

```
key - SONAR_HOST_URL
Value - http://13.201.134.147:9000
```

As of now, Two variables added.

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/f497b02d-7b14-4213-8c6b-79e2deb5b166)

Navigate to Sonarqube console  and click on continue

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/936f4e25-18fe-4069-b4fa-b00be8be1f16)

It will provide and CI configuration file copy it and use it inside our .gitlab-ci.yml file

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/c1164190-2179-466e-a790-d59a7addf59b)

Commit changes and it will automatically start the build.

Click on Build –> Pipelines

Your .gitlab-ci.yml file would be like below

```
stages:
    - npm
    - sonar

Install dependecy:
    stage: npm    
    image:
        name: node:16
    script:
        - npm install    

sonarqube-check:
  stage: sonar
  image: 
    name: sonarsource/sonar-scanner-cli:latest
    entrypoint: [""]
  variables:
    SONAR_USER_HOME: "${CI_PROJECT_DIR}/.sonar"  # Defines the location of the analysis task cache
    GIT_DEPTH: "0"  # Tells git to fetch all the branches of the project, required by the analysis task
  cache:
    key: "${CI_JOB_NAME}"
    paths:
      - .sonar/cache
  script: 
    - sonar-scanner
  allow_failure: true
  only:
    - main
```

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/0c1b78cf-c38b-4b6c-bf7b-678bb2a94768)

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/31f56ef7-5c92-446d-b8b9-c17486241761)

my job has been succeeded.

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/4da759d4-c14f-4a02-81a1-e51cf09df25b)

### Add Trivy file scan stage

Now add the next stage of the Trivy file scan

Update the .gitlab-ci.yml file

```
stages:
    - npm
    - sonar
    - trivy file scan

Install dependecy:
    stage: npm    
    image:
        name: node:16
    script:
        - npm install    

sonarqube-check:
  stage: sonar
  image: 
    name: sonarsource/sonar-scanner-cli:latest
    entrypoint: [""]
  variables:
    SONAR_USER_HOME: "${CI_PROJECT_DIR}/.sonar"  # Defines the location of the analysis task cache
    GIT_DEPTH: "0"  # Tells git to fetch all the branches of the project, required by the analysis task
  cache:
    key: "${CI_JOB_NAME}"
    paths:
      - .sonar/cache
  script: 
    - sonar-scanner
  allow_failure: true
  only:
    - main

Trivy file scan:
  stage: trivy file scan
  image:
    name: aquasec/trivy:latest
    entrypoint: [""]
  script:
    - trivy fs .
```

My job has been succeeded without fail.

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/50d91830-46a7-4575-8c1a-0613ddf70d99)

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/11afdcd7-fbad-4d71-bb82-1d293d70dc49)

If i go and check with sonarqube for code analysis

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/aad1650a-b5b5-4f9d-8b11-3d2d14773e6f)

### To add Docker stage

Add the Docker build and push stage

Before that Add docker credentials to GitLab Variables as secrets.

Go to the docker hub and create a Personal Access token

Navigate to Docker hub -> profile -> My profile -> Security -> New access token

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/34c91bf7-b1a4-4388-a63c-db7d8463b3de)

Navigate to Gitlab -> project repo -> Settings -> CICD -> Variables -> Expand

```
key - DOCKER_USERNAME
value - latchudevops

key - DOCKER_PASSWORD
value - aaaaaaaaaabbbbbbbbbccccccc
```

So we have 4 variables as of now,

![image](https://github.com/kohlidevops/gitlab-cicd/assets/100069489/50c72334-26ab-402b-99bb-4fcaed751e36)
