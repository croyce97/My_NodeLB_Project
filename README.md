

# My_NodeLB_Project



##  Overview
This project demonstrates how to deploy a Node.js application in Docker containers across two backend servers, and load balance them using an NGINX reverse proxy on a separate frontend server.


##  Project Architecture
+ VM1: 192.168.232.110: NGINX (load balancer)

+ VM2: 192.168.232.111: Node.js App (port 3001)

+ VM3: 192.168.232.113: Node.js App (port 3002)



##  Setup Instructions
###  Step 1: Install Docker on VM2 and VM3

```
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker
```

###  Step 2: Clone the project
`git clone https://github.com/croyce97/My_NodeLB_Project.git `

`cd My_NodeLB_Project`

### Step 3: Build Docker image and Run the container
VM2: 
+ `docker build -t my_node_app:v1 .`
+ `docker run -dp 3001:3000 my_node_app:v1`
  

VM3: 

+ `docker build -t my_node_app:v1 .`

+ `docker run -dp 3002:3000 my_node_app:v1`

### Step 4: Test each app individually on browsers (Chrome, Safari,...)
+ Visit: http://192.168.232.111:3001

+ Visit: http://192.168.232.113:3002

### Step 5: Install NGINX on VM1
```
sudo apt update
sudo apt install -y nginx
sudo systemctl enable nginx
```

### Step 6: Configure NGINX load balancing
+ Create `sudo nano /etc/nginx/sites-available/my_node_app.conf
` on VM1 using the [nginx.conf](https://github.com/croyce97/My_NodeLB_Project/blob/main/my_node_app.conf) provided in this repository.

+ `sudo ln -s /etc/nginx/sites-available/my_node_app /etc/nginx/sites-enabled/`



### Step 7: Log Backend IP (Upstream) in NGINX
* Open the main config file on VM1: `sudo vi /etc/nginx/nginx.conf `
* Inside the `http { ... } block`, add this custom log format:
```
log_format upstreamlog '$remote_addr - $remote_user [$time_local] '
                      '"$request" $status $body_bytes_sent '
                      '"$http_referer" "$http_user_agent" '
                      'to: $upstream_addr';

access_log /var/log/nginx/access.log upstreamlog;
```

### Step 8: Add local DNS mapping for `canhnq.com`
VM1: 

+ `sudo vi /etc/hosts`

+ Add `127.0.0.1  canhnq.com`

Local Windows Machine: 
+ Open Notepad as Administrator
+ Open the file: `C:\Windows\System32\drivers\etc\hosts`
+ Add: `192.168.232.110    canhnq`

### Step 9: Start NGINX and Monitor Logs
* Start or reload NGINX:
```
sudo nginx -t              
sudo systemctl start nginx
```
* Visit: `canhnq.com` and reload multiple times
* Monitor access logs real time:
`tail -f /var/log/nginx/access.log`



