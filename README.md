
### VPC
A virtual private cloud (VPC) is a virtual network dedicated to your AWS account. It is logically isolated from other virtual networks in the AWS Cloud. For this example we will use the default VPC.

Run the following command to find your default VPC details
```shell
    aws ec2 describe-vpcs --filters 'Name=tag:Name,Values=default'
```

:pencil2: Write in a notepad the VpcId , a number like this:

"VpcId": "vpc-0eb0be50ff77cde7c"


### VPC subnets
You can launch AWS resources into a specified subnet, we need this information for the LB

```shell
aws ec2 describe-subnets --filters "Name=vpc-id,Values=vpc-0eb0be50ff77c"
aws ec2 describe-subnets --filters "Name=vpc-id,Values=vpc-0eb0be50ff77c" | grep SubnetId
```

Copy in your notepad the result, the output looks similar as the following example

        "SubnetId": "subnet-02d89c59871601646",
        "SubnetId": "subnet-0d2e9c5c6db01b9bd",
        "SubnetId": "subnet-07e6405cf53c318c6",
        "SubnetId": "subnet-0055404616268fbf4",
        "SubnetId": "subnet-0a6ddc17b23850d5d",
        "SubnetId": "subnet-0ba157609bdf5641d",
