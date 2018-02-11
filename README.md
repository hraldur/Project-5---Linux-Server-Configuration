# Project 5 - Linux Server Configuration

* Public IP: 35.176.165.249 
* SSH Port: 220
* Url: http://ec2-35-176-165-249.eu-west-2.compute.amazonaws.com/


## Server
1. Create Instance
2. Download private key
3. `mv ~/privatekey.pem ~/.ssh`
4. `chmod 600 ~/.ssh/privatekey.pem`
5. `ssh -i ~/.ssh/privatekey.pem ubuntu@35.176.165.249`


## Create user
