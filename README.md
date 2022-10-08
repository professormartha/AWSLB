
### VPC
A virtual private cloud (VPC) is a virtual network dedicated to your AWS account. It is logically isolated from other virtual networks in the AWS Cloud. For this example we will use the default VPC.

Run the following command to find your default VPC details
```shell
    aws ec2 describe-vpcs --filters 'Name=tag:Name,Values=default'
```

:pencil2: Coopy on your notepad the VpcId,  the output looks similar as the following example:

> "VpcId": "vpc-0eb0be50ff77cde7c"


### VPC subnets
You can launch AWS resources into a specified subnet, we need the SubnetId for the LB, run the following command to find that information

```shell
aws ec2 describe-subnets --filters "Name=vpc-id,Values=vpc-XXXXXXXXXXXX"
aws ec2 describe-subnets --filters "Name=vpc-id,Values=vpc-XXXXXXXXXXXX" | grep SubnetId
```

Copy in your notepad the result, the output looks similar as the following example

>"SubnetId": "subnet-02d89c59871601234",<br />
>"SubnetId": "subnet-0d2e9c5c6db012345",<br />
>"SubnetId": "subnet-07e6405cf53c31234",<br />

### Security Group
Create a security group which controls the traffic that is allowed to reach and leave from the Load Balancer. Use the value of your VpcId you wrote in your notepad.
```shell
aws ec2 create-security-group --group-name MySecurityGroup --description "My security group LB" --vpc-id vpc-XXXXXXXXXXXX
```
:pencil2: Copy in your notepad the GroupId, the output looks similar as the following example
       
>"GroupId": "sg-0529e1920d4208f91"

Using the GroupId value, run the following command to authorized the HTTP protocol
```shell
aws ec2 authorize-security-group-ingress --group-id sg-XXXXXXXXXXXXX --protocol tcp --port 80 --cidr 0.0.0.0/0
```

### Load balancer
The load balancer and its target group must be in the same VPC and in the same Region as your Auto Scaling group.

#### Create a Target Group
By default, a load balancer routes requests to its targets using the protocol and port number that you specified when you created the target group.  Use the create-target-group command to create a target group, specify the same VpcId you have in your notepad

```shell
aws elbv2 create-target-group --name my-targets --protocol HTTP --port 80 \
--vpc-id vpc-XXXXXXXXXXXX --ip-address-type ipv4
```

:pencil2: Copy in your notepad the "TargetGroupArn" and "TargetGroupName". The output looks similar as the following example

> "TargetGroupArn": "arn:aws:elasticloadbalancing:us-east-1:137666369442:targetgroup/my-targets/3e562561419f5d1d", </br>
> "TargetGroupName": "my-targets",

#### Create a Load Balancer

The following create-load-balancer command creates a Load Blancer. Look at your notapad to find the values for all subnets and security group.

```shell
aws elbv2 create-load-balancer --name my-load-balanancer --subnets subnet-XXXXXXXXX subnet-XXXXXXXXX --security-groups sg-XXXXXXXXXX
```

:pencil2: Copy in your notepad the "LoadBalancerArn" and "DNSName". The output looks similar as the following example

>"LoadBalancerArn": "arn:aws:elasticloadbalancing:us-east-1:137666369442:loadbalancer/app/my-load-balanancer/09e9a24902fc24c5",</br>
>"DNSName": "my-load-balanancer-1802397733.us-east-1.elb.amazonaws.com",


### Auto Scaling group

The following create-auto-scaling-group command creates an Auto Scaling group with an attached target group. Specify the Amazon Resource Name (ARN) of a target group for an Application Load Balancer.

```shell
aws autoscaling create-auto-scaling-group --auto-scaling-group-name my-asg \
  --launch-template "LaunchTemplateName=TestMyTemplate,Version=1" \
  --vpc-zone-identifier "subnet-XXXX, subnet-XXXX, subnet-XXXXXX" \
  --target-group-arns "arn:aws:elasticloadbalancing:us-east-1:137666369442:targetgroup/my-targets/3e562561419f5d1d" \
  --max-size 4 --min-size 1 --desired-capacity 2
```





 
