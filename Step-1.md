## Step 1 - Containerize the Monolith

#### 1. Get Setup
In the next few steps, we are going to be using **Docker**, **Github**, **Amazon ECS**, and **Amazon ECR** to deploy code into containers. To make sure you can complete these steps, you'll need to ensure you have the right tools.

**Have an AWS Account**
If you don't already have an account with AWS, you can [sign up here](https://portal.aws.amazon.com/gp/aws/developer/registration/index.html). All the excercises in this tutorial are covered under [AWS Free Tier](https://aws.amazon.com/free/). *Note: some of the services we'll be using may require your account to be active for more than 12 hours. If you experience difficulty with any services and have a newly created account, please wait a few hours and try again*

**Install Docker**
We'll be using Docker to build the image files that we will run in our containers. Docker is an open source project and you can dowload it for free here [for mac](https://docs.docker.com/docker-for-mac/install/) or [for windows](https://docs.docker.com/docker-for-windows/install/).

Once docker is installed, you can check it's working by running `docker --version` in the terminal.
You should see something like this `Docker version 17.03.0-ce, build 60ccb22`.

**Install the AWS CLI**
* We'll use the AWS Command Line Interface (CLI) to push our images to Amazon EC2 Container Repository. Install the CLI [here](http://docs.aws.amazon.com/cli/latest/userguide/installing.html)

* Once the AWS CLI is installed, you can check it's working by running `aws --version` in the terminal.
You should see something like this: `aws-cli/1.11.63 Python/2.7.10 Darwin/16.5.0 botocore/1.5.26`.

* If you already have the AWS CLI installed, run the following command in the terminal to ensure it is updated to the latest version.
`pip install awscli --upgrade --user`

**Have a Text Editor**
If you don't already have a text editor for coding, we highly recommend installing one to your local environment. [Atom](https://atom.io/) is a simple, open-source text editor from GitHub that we like to use at AWS.

----

#### 2. Download and open the project
**Download code from GitHub**
Navigate to [https://github.com/awslabs/amazon-ecs-nodejs-microservices](https://github.com/awslabs/amazon-ecs-nodejs-microservices) and select 'Clone or Download' to download the GitHub repository to your local environment. You can also use [GitHub Desktop](https://desktop.github.com/) or [Git](https://git-scm.com/) to clone the repository.

**Open in Atom**
Start Atom, select 'Add Project Folder', and select the folder where you saved the repository 'amazon-ecs-nodejs-microservices'. This will add the entire project into Atom so you can easily work with it.

In your project folder, you should see folders for `infrastructure` and `services`. `infrastructure` holds the Cloudformation infrastructure configuration code we will use in the next step. `services` has the code that forms our node.js application.

Take a few minutes to click through the files and familiarize yourself with the different aspects of the application, including the database `db.json`, the server `server.js`, [npm dependancy declarations](https://docs.npmjs.com/how-npm-works/packages#what-is-a-package) `package.json`, and the [Dockerfile](https://docs.docker.com/engine/reference/builder/) `dockerfile`.

---
#### 3. Provision a repository using Amazon EC2 Container Registry (Amazon ECR)
**Create the repository**
* Navigate to the [Amazon ECR Console](https://us-west-2.console.aws.amazon.com/ecs/home?/repositories).
* Select **Create Repository**
* Name your repository. For this step, keep it simple and call this repository *api*.

**Record repository information**
* After you hit next, you should get a message that looks like this:
[repo image]
* The repository address follows a simple format:
[account-id].dkr.ecr.[region].amazonaws.com/[repo-name]
* *Take note of this address, including your account ID and the region you are using, you'll need it in the next steps*

---
#### 4. Prepare & Push the Docker Image
Open your terminal, and set your path to the `2-containerized/services/api` section of the GitHub code. `~/amazon-ecs-nodejs-microservices/2-containerized/api`

**Authenticate Docker Login with AWS**
1. Run `aws ecr get-login --no-include-email --region [region]`
*Example: `aws ecr get-login --no-include-email --region us-west-2`*
2. You're going to get a massive output starting with `docker login -u AWS -p` ... Copy this entire output, paste, and run it in the terminal.
3. You should see `Login Succeeded`

**Build the Image**
In the terminal, run `docker build -t api .` *Note, the `.` is important here.*

**Tag the Image**
After the build completes, tag the image so you can push it to the repository:
`docker tag [account-id].dkr.ecr.[region].amazonaws.com/api:latest`

**Push the image to ECR**
Run `docker push` to push your imaage to ECR:
`docker push [account-id].dkr.ecr.[region].amazonaws.com/api:latest`

If you navigate to your ECR repository, you should see your image tagged `latest`.
[image of image]
