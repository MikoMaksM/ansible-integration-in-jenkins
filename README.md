## CD - Deploy Application from Jenkins Pipeline to EC2 Instance (automatically with docker)

This project was a part of the TWN DevOps Bootcamp.

## Technologies used:
AWS, Jenkins, Docker, Linux, Git, Java, Maven, Docker Hub

## Project Description:
- Prepare AWS EC2 Instance for deployment (Install Docker)
- Create ssh key credentials for EC2 server on Jenkins
- Extend the previous CI pipeline with deploy step to ssh into the remote EC2 instance and deploy newly built image from Jenkins server
- Configure security group on EC2 Instance to allow access to our web application