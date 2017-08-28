## Step 3 - Break the Monolith

#### 1. Provision the ECR Repositories
In the previous two steps, we deployed our application as a monolith using a single service and a single container image repository. Now that we have broken that monolith into three microservices, we will need to provision three repositories (one for each service) in Amazon ECR. Our services are:
1. users
2. threads
3. posts

**Create the repository**
Repeat these steps for each microservice.
* Navigate to the [Amazon ECR Console](https://console.aws.amazon.com/ecs/home?#/repositories).
* Select **Create Repository**
* Repository name =
  * `users`
  * `threads`
  * `posts`
* Record the repositories information: `[account-id].dkr.ecr.[region].amazonaws.com/[service-name]`

You should have four repositories in Amazon ECR:

![image 3.1 - Repositories](images/3.1-repository,png)

---
#### 2. Authenticate Docker with AWS (optional)
You may skip this step if you recently completed [Part 1](/getting-started/container-microservices-tutorial/step-one/) of this workshop.
1. Run `aws ecr get-login --no-include-email --region [region]`
*Example: `aws ecr get-login --no-include-email --region us-west-2`*
2. You're going to get a massive output starting with `docker login -u AWS -p` ... Copy this entire output, paste, and run it in the terminal.
3. You should see `Login Succeeded`

---
#### 3. Build and push images for each service
In the project folder `amazon-ecs-nodejs-microservices/3-microservices/services`, you will have folders with files for each service. Notice how each microservice is essentially a clone of the previous monolithic service.

You can see how each service is now specialized by comparing the file `db.json` in each service and in our monolithic `api` service. Previously posts, threads, and users were all stored in a single database file. Now, each is stored in the database file for its respective service.

Open your terminal, and set your path to the `3-microservices/services` section of the GitHub code. `~/amazon-ecs-nodejs-microservices/3-microservices/services`

**Build and Tag Each Image**
Repeat these steps for each microservice image.

* In the terminal, run `docker build -t [service-name] ./[service-name]`
_Example `docker build -t posts ./posts`_
* After the build completes, tag the image so you can push it to the repository:
`docker tag [service-name]:latest [account-id].dkr.ecr.[region].amazonaws.com/[service-name]:latest`
_example: `docker tag posts:latest [account-id].dkr.ecr.us-west-2.amazonaws.com/posts:latest`_
* Run `docker push` to push your imaage to ECR:
`docker push [account-id].dkr.ecr.[region].amazonaws.com/[service-name]:latest`

If you navigate to your ECR repository, you should see your image tagged `latest`.

**NOTE: Be sure to build and tag all three images.**

#### [Next](/Step-4.md)
