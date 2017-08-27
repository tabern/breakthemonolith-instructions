## Step 5 - Clean Up

#### 1. Turn off your services
Start cleaning up by spinning down each of the four services you have running across your two clusters.
* Monolith Cluster
  * api
* Microservices Cluster
  * posts
  * threads
  * users

**Update Service**
* Open your cluster and click into a service.
* Select `Update`
* Change `Number of tasks` to `0`
* Select `Update Service`
* Select `View Service` to go back

**Delete Service**
* Select `Delete` on the service console page and confirm.
* You will get a confirmation message and see your cluster empty.

[5.1 - Service Delete Confirmation]

Repeat these steps for each of your services across both clusters.

[5.1 - Empty Cluster]

---
#### 2. Delete listeners
* Navigate to the [Load Balancer section of the EC2 Console](https://console.aws.amazon.com/ec2/v2/home?#LoadBalancers:)
* Select a load balancer and select the **Listeners** tab.
* Select the listener, then `Actions` > `Delete`
* Confirm delete. Your listener should be gone.

**Repeat this step for both load balancers.**
* demo-monolith
* demo-microservices

[5.2 - Delete Listeners]

---
#### 3. Delete target groups
* Navigate to [Target Groups](https://console.aws.amazon.com/ec2/v2/home?#TargetGroups:) in the EC2 console.
* Check the box at the top of the list to select all target groups.
* Select `Actions` > `Delete`.
* Confirm Delete.

[5.3 - Delete Target Groups]

---
#### 4. Rollback your Cloudformation Stack
* Navigate to the CloudFormation console: https://console.aws.amazon.com/cloudformation/home?
* Check the box next to one of the Cloudformation stacks you created earlier
  * Nodejs-Monolith
  * Nodejs-Microservices
* Select `Actions` > `Delete Stack`
* Confirm Delete.
* The stack status should change to `DELETE_IN_PROGRESS`
* Repeat for the remaining stack.

**WARNING! Be sure to delete both stacks. Leaving a stack running will result in charges on your AWS account.**

[image 5.4 - Delete CF]

---
#### 5. Delete you Amazon ECR Repositories
* Navigate to [Repositories](https://console.aws.amazon.com/ecs/home?#/repositories) in the Amazon ECR console.
* Check the box at the top of the list to select all repositories
* Select `Delete Repository` and confirm.
* You will see a confirmation message and should have no more repositories.

[image 5.5 - Delete Repos]
