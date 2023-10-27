# Load-Balancing-and-NGINX
## Introduction to load balancing and Nginx

Load balacing is team work for compters. 
Loading balancing means distributing the work or tasks across multiple computers or servers sos that no one computer gets overloaded with too much work.
Loading balancing means distributing the work or tasks across multiple computers or servers sos that no one computer gets overloaded with too much work.

![image](https://github.com/Bukolaogunwale1/Load-Balancing-and-NGINX/assets/122865359/42cba7d9-d772-4716-98c7-5695b5507173)

A load balancer acts as the “traffic cop” sitting in front of your servers and routing client requests across all servers capable of fulfilling those requests in a manner that maximizes speed and capacity utilization and ensures that no one server is overworked, which could degrade performance.
Nginx is a versertile software that can be configured to act like a webserver; reverse proxy and a load balancer.

**step 1:** Positioning ec2 Instance

![image](https://github.com/Bukolaogunwale1/Load-Balancing-and-NGINX/assets/122865359/1237f4b2-55e7-47bc-861f-6bd479c7d7d7)

**step 2:** run two webservers on port 8000 while the load balancer runs on port 80. open port 8000 to allow traffic from anywhere.

<img width="778" alt="the intances" src="https://github.com/Bukolaogunwale1/Load-Balancing-and-NGINX/assets/122865359/e6aaaed1-c1d0-4af1-a5f9-66b4523d607e">

![image](https://github.com/Bukolaogunwale1/Load-Balancing-and-NGINX/assets/122865359/87a0f11b-e437-4356-8054-5983d3a40372)

- open terminal to paste the ssh command to be connected to terminal
- then click

  <img width="479" alt="ssh for 1" src="https://github.com/Bukolaogunwale1/Load-Balancing-and-NGINX/assets/122865359/56e30f01-06ac-464b-a482-b232c39151f8">

- Install apache with this command
  ```
  sudo apt update -y &&  sudo apt install apache2 -y
  ```
  
  <img width="946" alt="sudo apt insatll and for 1" src="https://github.com/Bukolaogunwale1/Load-Balancing-and-NGINX/assets/122865359/cf5ff6bd-d09b-48a1-9c74-c7d96b9ed952">

  - To verify taht apache is workin
   
   ```
   sudo systemctl status apache2
   ```

<img width="940" alt="sudo systemct for 1" src="https://github.com/Bukolaogunwale1/Load-Balancing-and-NGINX/assets/122865359/0009092b-589d-42fc-a857-45242378343c">

**Step 4:** Configure Apache to serve a page showing it public IP  
Configure Apache webserver to serve on port 8000 instead of its defaultbwhich is port 80. then a bew index .html file is created, the file will contain code to display puplic ipbof the Ec2 instance.Apache webserver's default bis over ridden with our new html

- configuring Apache to serve content on port 8000
    1. using your text editor (eg vi, nano) open the file/etc/apache.port.conf
    
    ```
    sudo vi /etc/apache2/ports.conf 
    ```
    
    2. Add a new listen directive for port 8000 the save

  ![image](https://github.com/Bukolaogunwale1/Load-Balancing-and-NGINX/assets/122865359/b4bd243b-6cfb-448c-a996-3389c7d6cc71)

    3. open the file/ect/apache2/sites-availble/000-deauilt.cong and change port 80 on the virtual host
    
    ```
    sudo vi /etc/apache2/sites-available/000-default.conf
    ```
    
![image](https://github.com/Bukolaogunwale1/Load-Balancing-and-NGINX/assets/122865359/5b50b5d6-594f-4c96-9f73-9c9bbe9ea810)

5. Restart apache 

```
sudo systemctl restart apache2
```

![image](https://github.com/Bukolaogunwale1/Load-Balancing-and-NGINX/assets/122865359/df62ef50-ec80-4054-829e-7cea796db29d)

- creating our new html file:

  1. open a new **indext.html** file
     
```
sudo vi index.html
```

2. replace the placeholder text for IP adress in the html file{ PUT public IP number from your ec2 in the html below }

```
        <!DOCTYPE html>
        <html>
        <head>
            <title>My EC2 Instance</title>
        </head>
        <body>
            <h1>Welcome to my EC2 instance</h1>
            <p>Public IP: YOUR_PUBLIC_IP</p>
        </body>
        </html>
```
3. Change file ownership of the index.html file
   ```
   sudo chown www-data:www-data ./index.html
   ```
   <img width="948" alt="chown and" src="https://github.com/Bukolaogunwale1/Load-Balancing-and-NGINX/assets/122865359/314189e2-18d9-4af7-84af-9dbdfd00b6ee">

   - Overriding the default html file with new html file
   1. Replace the default html file with our new html

   ```
   sudo cp -f ./index.html /var/www/html/index.html
   ```
   2.Restart the webserver to load new configuration
   
   ```
   sudo systemctl restart apache2
   ```

 ![image](https://github.com/Bukolaogunwale1/Load-Balancing-and-NGINX/assets/122865359/43493b40-169c-4c6c-b978-7a5d9f82a3ea)

 **Step 5:** Configuring Ngnix as a load balancer
        - The third EC2 Instance has port 80 opened to accept traffic from anywhere
        - SSH into the instance
        - Install Ngnix into the instance 

 ```
 sudo apt update -y && sudo apt install nginx -y
 ```
      
  <img width="945" alt="sudo apt install ngnix" src="https://github.com/Bukolaogunwale1/Load-Balancing-and-NGINX/assets/122865359/4e65a170-a9ee-442c-b916-6888e989078b">

   - To verify that ngnix is installed 
   
 ```
sudo systemctl status nginx
```
![image](https://github.com/Bukolaogunwale1/Load-Balancing-and-NGINX/assets/122865359/b3692d58-8f7e-428b-b620-dda55d74d353)

-Open Nginx configuration file 
```
sudo vi /etc/nginx/conf.d/loadbalancer.conf
```
- Paste the configuration file (chane the public Ip address of web 1 and web 2 to your own also put the Ip adress of your load balancer inthe 2nd paragraph)

  ```
         upstream backend_servers {

            # your are to replace the public IP and Port to that of your webservers
            server 3.14.134.153:8000; # public IP and port for webserser 1
            server 13.58.64.167:8000; # public IP and port for webserver 2

        }

        server {
            listen 80;
            server_name 18.119.253.147; # provide your load balancers public IP address

            location / {
                proxy_pass http://backend_servers;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            }
        }
  
    ```
  
  - To test configuration
    ```
    sudo nginx -t
    ```
    <img width="945" alt="ngnix -t" src="https://github.com/Bukolaogunwale1/Load-Balancing-and-NGINX/assets/122865359/64076ab5-07ac-4be8-a71b-435dec7df798">

    - Paste the public IPadress for Nginx load balancer, you shouldsee the samewebpages served by webservers.
    
![image](https://github.com/Bukolaogunwale1/Load-Balancing-and-NGINX/assets/122865359/ff3a16d1-5c98-49ca-a44a-f162acc29fb2)



        


  
   











