2) Terraform

You have the following terraform code:

provider "aws" {
   region = "us-west-2"
}

resource aws_instance "instances" {
   count = 3

   ami = "ami-e689729e"

   instance_type = "t2.micro"
   subnet_id = [ "subnet-1234" ]
}

resource aws_elb "elb" {
   name = "sample-elb"

   subnets = [ "subnet-1234" ]

   listener {
     instance_port = 8080
     instance_protocol = "HTTP"
     lb_port = 80
     lb_protocol = "HTTP"
   }
}

resource aws_elb_attachment "elb" {
   count = 3

   elb = "${aws_elb.elb.id}"
   instance = "${element(aws_elb.elb.*.id, count.index)}"
}

At a high level what does the above accomplish/create?
