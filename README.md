# haproxy
Create an haproxy with multiple webservers using ansible 

> If you want to try out this on your own, you will need: 
- vagrant 
- virtualbox  
- internet connection (for downloading the box)

> For production, you will need: 
- just an ansible envrionment

### environment 
laptop -- control -- lb -- webservers ( * 4) 

The `Vagrantfile` will create 6 virtual machine in total. 
- control: run all the ansible playbook to control the lb and webservers
- lb: traffic control 
- webservers: handle http requests 


