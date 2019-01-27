---
layout: page
title: "How to: set up AWS"
date: 2018-11-09
---

Setting up AWS. For free tier access we can choose the t2.micro instance, Amazon Linux AMI.

Storage: max out the EBS storage at 30GB. We can have one root EBS (8GB) which is deleted on termination, and a second EBS (/dev/sdb) of 22GB which persists on termination; this is for storing our files in. 

We must expose the ports that we need. 

	SSH - TCP - 22 (My IP)
	HTTPS - TCP 443 (My IP)
	Custom TCP - TCP - 8888 (My IP)

Create new key pair, and save to .ssh. Run

	chmod 400 key.pem

to change permission to single user read-only. Then ssh to instance:

	ssh -i 'path-to-key' ec2-user@...publicDNS... 

Then start installing stuff:
	
	sudo yum update -y
	sudo yum install -y docker
	sudo service docker start
	sudo usermod -a -G docker ec2-user

	yum install git

	docker system prune 

	