### Example main.tf

Explanation and details outlined here as comments:


```java
# Provider sets the IaaS provider to use for this build
provider "aws" {
   region = "us-west-2" # the region in aws to use
}

# This creates ec2 resources within aws. This resource is identified by "instances"
resource aws_instance "instances" {
   count = 3 # create 3 resources or ec2 instances

   ami = "ami-e689729e" # use this ami to create the resources

   instance_type = "t2.micro" # sets the instance type. Instance type is dependent upon region. 
   subnet_id = "subnet-1234"
/* subnet_id can't be a list in this resource. 
A list will generate these errors: 
* aws_instance.instances[0]: subnet_id must be a single value, not a list
* aws_instance.instances[1]: subnet_id must be a single value, not a list
* aws_instance.instances[2]: subnet_id must be a single value, not a list
*/
}

# Creates an ELB
resource aws_elb "elb" {
   name = "sample-elb"

   subnets = [ "subnet-1234" ] # list of subnets to attach

   listener { # listener block
     instance_port = 8080 # port to route to
     instance_protocol = "HTTP"
     lb_port = 80 # port to listen to for the load balancer
     lb_protocol = "HTTP"
   }
}

resource aws_elb_attachment "elb" { # creates 3 load balancer attachments
   count = 3

   elb = "${aws_elb.elb.id}" # the name of the elb is gathered from the aws_elb resource above named "elb". 
   instance = "${element(aws_elb.elb.*.id, count.index)}" # The instance ID is created sequentially and is unique
}
```
