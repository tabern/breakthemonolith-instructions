## Step 4 - Deploy Microservices

#### 1. Launch an ECS Cluster using Cloudformation
Now, we'll create a an Amazon ECS cluster, deployed behind an Application Load Balancer to host our microservices.

1. Navigate to the CloudFormation console: https://us-west-2.console.aws.amazon.com/cloudformation/home?
2. Select `Create Stack`
3. Select 'Upload a template to Amazon S3' and choose the `ecs.yml` file from the GitHub project at `amazon-ecs-nodejs-microservice/3-containerized/infrastructure/ecs.yml`
Select `Next`
4. For stack name, enter `Nodejs-Microservices`
Keep the other parameter values the same:
`Desired Capacity = 2`
`InstanceType = t2.micro`
`MaxSize = 2`
Select `Next`.
5. It is not nessecary to modify any options on this page. Select `Next`.
6. Check the box at the bottom of the next page and select `Create`.
You will see your stack with the orange `CREATE_IN_PROGRESS`. You can select the refresh button at the top right of the screen to check on the progress. This process typically takes under 5 minutes.

**Pro Tip**
You can also use the AWS CLI to deploy Cloudformation Stacks. Just add in your region to this code and run in the terminal from `amazon-ecs-nodejs-microservices/3-microservices`.
```
$ aws cloudformation deploy \
   --template-file infrastructure/ecs.yml \
   --region <region> \
   --stack-name Nodejs-Microservices \
   --capabilities CAPABILITY_NAMED_IAM
```

----
### 2. Check your cluster is running.

* Navigate to the [Amazon ECS console](https://console.aws.amazon.com/ecs/home?). Your cluster should appear in the list.
[cluster image]

* Clicking into the cluster, select the 'Tasks' tab, no tasks will be running.
[tasks image]

* Select the 'ECS Instances' tab, you will see the two EC2 Instances the Cloudformation template created.
[instances image]

---
_to be continued..._

#### [Next](/Step-5.md)
