# Automating Loadbalancer configuration with Shell scripting

This project demostrates how to automate the setup and maintenance of load balancer using freestyle job, enhancing efficiency and reducing manual effort.

#  Automate the Deployment of Webservers

Automation helps us speed the deployment of services and reduce the chance of making errors in out day to dat activity. We will write shell scripts that makes our work faster and efficient in this projects.

# Deploying and configuring the Webservers

Here's the step by step guide to deploy webservers.

<b>Step 1:</b> Launch an EC2 instance running on ubuntu 20.04.

![webserver](./imgs/webs%201.png)

<b>Step 2:</b> Open port 8000 to allow traffic from anywhere using the security group. 

![security group](./imgs/security%202.png)

<b>Step 3:</b> Connect to the webserver via the terminal using SSH client

![connection](./imgs/connect%203.png)

<b>Step 4:</b> Open a file, paste the script below and  save it.
![script](./imgs/script%204.png)

<b>step 5:</b> Change the permission on the file to make an executable using the command below:

`sudo chmod +x install.sh

![chmod](./imgs/change%20mode%205.png)

Run the shell script using the command bellow.

`    ./install.sh PUBLIC_IP`

![run](./imgs/runninng%2066.png)


# Deployment of Nginx as a Load Balancer usin g shell script

### Automate the Deployment of Nginx as a Load Balancer using Shell script

Having successfully deployed and configured two webservers. we will move on to the load balancer. Follow the step above to launch an EC2 instance running ubuntu 22.o4. open port 80 in the security group to allow connection from anywhere using. Connect to the load balancer via terminal.

Follow the steps below:

<b>Step 1:</b> On your terminal, open a file nginx.sh using the command below:

`sudo vi nginx.sh`



<b>Step 2:</b> Copy and paste the ecript inside the file


```
#!/bin/bash
######################################################################################################################
##### This automates the configuration of Nginx to act as a load balancer
##### Usage: The script is called with 3 command line arguments. The public IP of the EC2 instance where Nginx is installed
##### the webserver urls for which the load balancer distributes traffic. An example of how to call the script is shown below:
##### ./configure_nginx_loadbalancer.sh PUBLIC_IP Webserver-1 Webserver-2
#####  ./configure_nginx_loadbalancer.sh 127.0.0.1 192.2.4.6:8000  192.32.5.8:8000
############################################################################################################# 
PUBLIC_IP=$1
firstWebserver=$2
secondWebserver=$3

[ -z "${PUBLIC_IP}" ] && echo "Please pass the Public IP of your EC2 instance as the argument to the script" && exit 1

[ -z "${firstWebserver}" ] && echo "Please pass the Public IP together with its port number in this format: 127.0.0.1:8000 as the second argument to the script" && exit 1

[ -z "${secondWebserver}" ] && echo "Please pass the Public IP together with its port number in this format: 127.0.0.1:8000 as the third argument to the script" && exit 1

set -x # debug mode
set -e # exit the script if there is an error
set -o pipefail # exit the script when there is a pipe failure


sudo apt update -y && sudo apt install nginx -y
sudo systemctl status nginx

if [[ $? -eq 0 ]]; then
    sudo touch /etc/nginx/conf.d/loadbalancer.conf

    sudo chmod 777 /etc/nginx/conf.d/loadbalancer.conf
    sudo chmod 777 -R /etc/nginx/

    
    echo " upstream backend_servers {

            # your are to replace the public IP and Port to that of your webservers
            server  "${firstWebserver}"; # public IP and port for webserser 1
            server "${secondWebserver}"; # public IP and port for webserver 2

            }

           server {
            listen 80;
            server_name "${PUBLIC_IP}";

            location / {
                proxy_pass http://backend_servers;   
            }
    } " > /etc/nginx/conf.d/loadbalancer.conf
fi
# restarting Nginx so it will read
sudo nginx -t
sudo systemctl restart nginx 
```


<b>Step 3:</b> Close the file using the command below 

`type esc the shift + :wqa!
`

<b>Step 4:</b> Change the file permision to make it executable.

`sudo chmod +x nginx.sh
`


<b>Step 5:</b> Run the script with the command below:

`./nginx.sh PUBLIC_IP Webserver-1 Webserver-2
`


