# Terraform-KickStart
Terraform Basic Tutorial
resource “aws_vpc” “myvpc” {
	Cidr_block :10.2.0.0/16
	Provider =aws.aws-west-1 // alias name used here from provider , it is meta arguments or a special arguments
tags={
“Name”=”vpx-west-1”	
}
}
