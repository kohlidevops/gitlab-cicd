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
