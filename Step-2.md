## Step 2 - Deploy the Monolith

#### 1. Launch an ECS Cluster using AWS Cloudformation
First, we'll create a an Amazon ECS cluster, deployed behind an Application Load Balancer.

1. Navigate to the CloudFormation console: https://console.aws.amazon.com/cloudformation/home?.
2. Select `Create Stack`.
3. Select 'Upload a template to Amazon S3' and choose the `ecs.yml` file from the GitHub project at `amazon-ecs-nodejs-microservice/2-containerized/infrastructure/ecs.yml`
Select `Next`.
4. For stack name, enter `BreakTheMonolith-Demo`.
Keep the other parameter values the same:
`Desired Capacity = 2`
`InstanceType = t2.micro`
`MaxSize = 2`
Select `Next`.
5. It is not necessary to modify any options on this page. Select `Next`.
6. Check the box at the bottom of the next page and select `Create`.
You will see your stack with the orange `CREATE_IN_PROGRESS`. You can select the refresh button at the top right of the screen to check on the progress. This process typically takes under 5 minutes.

![image 2.1 - CF Creation](images/2.1-stack.png)

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
#### 2. Check your cluster is running.

* Navigate to the [Amazon ECS console](https://console.aws.amazon.com/ecs/home?). Your cluster should appear in the list.
![image 2.2 - Cluster Active](images/2.2-cluster.png)

* Clicking into the cluster, select the 'Tasks' tab, no tasks will be running.
![image 2.2 - Tasks](images/2.2-tasks.png)

* Select the 'ECS Instances' tab, you will see the two EC2 Instances the Cloudformation template created.
![image 2.2 - Instances](images/2.2-instances.png)

---
#### 3. Write a Task Definition
The task definition tells Amazon ECS how to deploy our application containers across the cluster.

* Navigate to the 'Task Definitions' menu on the left side of the Amazon ECS console.
* Select `Create new Task Definition`.
* `Task Definition Name = api`.
* Select `Add Container`.
* Specify the following parameters. If a parameter is not defined, leave it blank or with the default settings:
`Container name = api`
`image = [account-id].dkr.ecr.[region].amazonaws.com/api:v1` the URL of your ECR repository image from the last step of this tutorial. Be sure the tag `:v1` matches the value you used in part 1 to tag and push the image.
`Memory = Hard limit: 256`
`Port mappings = Host port:0, Container port:3000`
`CPU units = 256`
* Select`Add`.
* Select `Create`.
* Your Task Definition will now show up in the console.

![image 2.3 - Task Definition](images/2.3-definition.png)

---
#### 4. Configure the Application Load Balancer: Target Group
The [Application Load Balancer (ALB)](http://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html) lets our service accept incoming traffic. The ALB automatically routes traffic to container instances running on our cluster using them as a [target group](http://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html).

**Check your VPC Name**
If this is not your first time using this AWS account, you may have multiple VPCs. It is important to configure your Target Group with the correct VPC.
* Navigate to the [Load Balancer section of the EC2 Console](https://console.aws.amazon.com/ec2/v2/home?#LoadBalancers:).
* You should see a Load Balancer already exists named `demo`.
* Select the checkbox to see the Load Balancer details.
* Note the value for the `VPC` attribute on the details page.

![image 2.4 - VPC](images/2.4-vpc.png)

**Configure the ALB Target Group**
* Navigate to the [Target Group section of the EC2 Console](https://console.aws.amazon.com/ec2/v2/home?#TargetGroups:).
* Select `Create target group`.
* Configure the Target Group (do not modify defaults if they are not specified here):
`Name = api`
`Protocol = HTTP`
`Port = 80`
`VPC =` _select the VPC that matches your Load Balancer from the previous step. This is most likely NOT your default VPC._
**Advanced health check settings**:
`Healthy threshold = 2`
`Unhealthy threshold = 2`
`Timeout = 5`
`Interval = 6`
* Select `Create`.

![image 2.4 - Target Group](images/2.4-targets.png)

---
#### 5. Configure the Application Load Balancer: Listener
The [listener](http://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-listeners.html) checks for incoming connection requests to your ALB.

**Add a Listener to the ALB**
* Navigate to the [Load Balancer section of the EC2 Console](https://console.aws.amazon.com/ec2/v2/home?#LoadBalancers:)
* You should see a Load Balancer already exists named `demo`.
* Select the checkbox to see the Load Balancer details.
* Select the `Listeners` tab.
* Select `Create Listener`:
Protocol = `HTTP`
Port = `80`
Default target group = `api`
* Click `Create`.

![image 2.5 - Listener](images/2.5-listener.png)

---
#### 6. Deploy the Monolith as a Service
Now, we'll deploy the monolith as a service onto the cluster.

* Navigate to the 'Clusters' menu on the left side of the Amazon ECS console.
* Select your cluster: `BreakTheMonolith-Demo-ECSCluster`.
* Under the services tab, select `Create`.
* Configure the service (do not modify any default values):
Service name = `api`
Number of tasks = `1`
* Select `Configure ELB`:
  * ELB Type = `Application Load Balancer`.
  * For IAM role, select `BreakTheMonolith-Demo-ECSServiceRole`.
  * Select your Load Balancer `ELB name = demo`.
  * Select `Add to ELB`.
* Add your service to the target group:
Listener port = `80:HTTP`
Target group name = select your group: `api`
* Select `Save`.
![image 2.6 - Service Configuration](images/2.6-config.png)
* Select `Create Service`.
* Select `View Service`.

![image 2.6 - Running Service](images/2.6-service.png)

Nice work! You now have a running service. It may take a minute for the container to register as healthy and begin receiving traffic.

---
#### 7. Test your monolith
Validate your deployment by checking if the service is available from the internet and pinging it.

**To find your service URL:**
* Navigate to the [Load Balancers](https://console.aws.amazon.com/ec2/v2/home?#LoadBalancers:) section of the EC2 Console.
* Select your load balancer `demo`.
* Copy and paste the value for `DNS name` into your browser.
* You should see a message 'Ready to receive requests'.

**See each part of the service**
The node.js application routes traffic to each worker based on the URL. To see a worker, simply add the worker name `api/[worker-name]` to the end of your DNS Name like this:
* `http://[DNS name]/api/users`
* `http://[DNS name]/api/threads`
* `http://[DNS name]/api/posts`

You can also add a record number at the end of the URL to drill down to a particular record. Like this: `http://[DNS name]/api/posts/1` or `http://[DNS name]/api/users/2`

![image - 2.7 - User Record](images/2.7-users.png)

#### [Next](/Step-3.md)
