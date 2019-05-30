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


### setup ansible on the control machine 
```bash
lingjie$ vagrant ssh control 
[vagrant@control ~]$ sudo su -
Last login: Thu May 30 03:43:36 UTC 2019 on pts/0
[root@control ~]# 
yum install ansible -y 

# make a project direcoty where we will run our playbooks 
mkdir ha && cd ha 

cat << EOF >>inventory  
[lb]
10.0.0.11 
[web]
10.0.0.2[1:4]
EOF
```

> note: 
For centos machines, password login is disabled, so I manually copied the ssh-keys over, this can be done by using a shell provision script in the Vagrant file, I didn't do that since I already started the machines and feeling lazy :P 

1. run the deploy-web-server.yml to configure the httpd on the web servers 
```yml
--- 
- name: install httpd and config the httpd content 
  hosts: web
  tasks: 
  - name: install httpd 
    yum: 
      name: "{{item}}" 
      state: latest
    with_items: 
    - mod_ssl 
    - httpd 

  - name: config httpd 
    copy:
      content: "{{inventory_hostname}}"
      dest: "/var/www/html/index.html"
    notify: 
    - restart httpd 

  handlers: 
  - name: restart httpd 
    service: name=httpd state=restarted 
```

2. configure the haproxy server 
