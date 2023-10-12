# Implementing_Load_Balancers_with_Nginx

**Introduction to Load Balancing and NGINX**

Load balancing is a crucial concept in computer networking and web services, while Nginx is a popular software application that can be used as a load balancer among other things. Let's start with an introduction to load balancing and then delve into how Nginx can be used for this purpose.

**Load Balancing:**

Load balancing is the process of distributing network traffic or application requests across multiple servers or resources to ensure optimal utilization, high availability, and improved performance. It plays a vital role in preventing server overload and improving the reliability and responsiveness of web applications and services.

Here are some key benefits of load balancing:

1. **High Availability**: Load balancers distribute traffic across multiple servers. If one server fails, traffic is automatically redirected to the healthy servers, minimizing downtime.

2. **Scalability**: As traffic grows, you can add more servers to your infrastructure and easily integrate them into the load-balancing pool.

3. **Improved Performance**: Load balancers can route requests to the server with the least current load, ensuring that no single server becomes a bottleneck.

4. **Traffic Management**: Load balancers can route specific types of requests to different servers based on predefined rules. For example, you can send image requests to one server and database queries to another.

**Nginx:**

Nginx (pronounced "engine-x") is a powerful and lightweight open-source web server, reverse proxy server and load balancer. It's known for its high performance, stability, and rich feature set. Nginx can be used as a load balancer by distributing incoming web traffic across multiple backend servers. Here's how it works:

1. **HTTP Load Balancing**: Nginx can distribute HTTP/HTTPS requests to a pool of backend web servers. It uses various load-balancing algorithms (e.g., round-robin, least connections, IP hash) to determine how requests are distributed.

2. **TCP/UDP Load Balancing**: Nginx can also handle TCP and UDP traffic, making it suitable for load-balancing non-HTTP services like databases, mail servers, or custom protocols.

3. **Health Checks**: Nginx continuously monitors the health of backend servers. If a server becomes unavailable or unresponsive, Nginx will automatically stop sending traffic to that server.

4. **Session Persistence**: Nginx can maintain session persistence, ensuring that requests from the same client are consistently routed to the same backend server, which is essential for some applications.

5. **SSL Termination**: Nginx can offload SSL/TLS encryption and decryption, reducing the computational load on backend servers.

Using Nginx as a load balancer is a common practice in high-traffic web applications and services. It can be configured to meet specific requirements and is often used in conjunction with other tools and technologies to create robust, scalable, and fault-tolerant infrastructure.

In summary, load balancing is a critical strategy for optimizing the performance and reliability of web applications, and Nginx is a versatile and powerful tool that can serve as an effective load balancer in your infrastructure.

**ARCHITECTURAL DIAGRAM**

![diagram](https://github.com/Ukdav/Implementing_Load_Balancers_with_Nginx/assets/139593350/31308a68-9149-4a76-8428-23fb57279fc0)

**SETTING UP A BASIC LOAD BALANCER**

We are going to be provisioning two EC2 instances running Ubuntu 22.04 and installing an apache webserver in them. We will open port 8000 to allow traffic from anywhere and finally update the default page of the webservers to display their public IP address.

Next, we will provision another EC2 instance running Ubuntu 22.04, this time we will install Nginx and configure it to act as a load balancer distributing traffic across the webserver.

**Step 1:** Provisioning the two EC2 Instance:

![2 EC2 instance](https://github.com/Ukdav/Implementing_Load_Balancers_with_Nginx/assets/139593350/d5b6222d-5310-497c-a3da-bc2b4d9adb5c)

**Step 2:** Editing inbound rule and Open port 8000, we will be running our webservers on port 8000 while the load balancers run on port 80. we need to open port 8000 to allow traffic from anywhere. to do this we need to add a rule to the security group of each of our web servers.

![editing inbound rules](https://github.com/Ukdav/Implementing_Load_Balancers_with_Nginx/assets/139593350/5cf073f7-08ee-473b-ae9f-216ffa98123c)

**Step 3:** Installing APache Webserver

After the provisioning, of both of the servers and having opened the necessary ports, it's time to install Apache software on both servers. to do this, we must first connect to each of the webservers via shh. Then we can now run the commands on the terminal of our web servers

* Connecting to the web server: click on the instance ID at the top of the page and click on connect, you will copy the SSH.

![ssh connect](https://github.com/Ukdav/Implementing_Load_Balancers_with_Nginx/assets/139593350/f553ff7d-0ac7-4987-b95e-170516ccf21f)

![via Ssh on terminal](https://github.com/Ukdav/Implementing_Load_Balancers_with_Nginx/assets/139593350/26ec63d4-e97e-4837-a8d6-c81c164308ed)

Next is to install the Apache Server:

Using this command: *sudo apt update -y &&  sudo apt install apache2 -y*

![using the command to install apache](https://github.com/Ukdav/Implementing_Load_Balancers_with_Nginx/assets/139593350/fa9eef7c-dec0-44c2-984a-7230baeb0481)

![server2](https://github.com/Ukdav/Implementing_Load_Balancers_with_Nginx/assets/139593350/b7f0bd7e-12ea-4389-a739-b7c97fd5ac2c)

To verify that Apache2 is running properly use this command: *sudo systemctl status apache2*

![Ubuntuapache-1](https://github.com/Ukdav/Implementing_Load_Balancers_with_Nginx/assets/139593350/45d8c39a-e660-4c92-b123-c67a5da5dac9)

![UbuntuApache-2](https://github.com/Ukdav/Implementing_Load_Balancers_with_Nginx/assets/139593350/d6d44e2a-5a12-4078-889c-902009127732)

step 4: configure Apache to server a page showing its public IP:

we will start by configuring the Apache webserver to server content on port 8000 instead of its default which is port 80. then we will create a new index.html file. the file will contain code to display the public IP of the EC2 instance. we will then override the Apache2 web server's default html file with our new file.

* Configuring Apache to server content on port 8000:

1. Use a text editor (e.g VI or NANO) to open the file */etc/apache2/ports.conf*

*sudo vi /etc/apache2/ports.conf*

2. Add a new listen directive for port 8000: First type i to switch the editor to insert mode. Then add the listen directive after which save your file.

![Sudo Vi apache2](https://github.com/Ukdav/Implementing_Load_Balancers_with_Nginx/assets/139593350/b78781f3-aede-400d-a04e-a3a31b533460)

![sudo vi apache1](https://github.com/Ukdav/Implementing_Load_Balancers_with_Nginx/assets/139593350/ae4f7b58-fc64-4fee-b0de-3634f8c6b683)

3. Next open the file */etc/apache2/sites-available/000-default.conf and change port 80 on the virtualhost to 8000 like the screenshot below:

copy below code:

*sudo vi /etc/apache2/sites-available/000-default.conf*

![virtualhost 1](https://github.com/Ukdav/Implementing_Load_Balancers_with_Nginx/assets/139593350/78a24ecd-9657-415a-94bd-b5661a864e1c)

![virtualhost 2](https://github.com/Ukdav/Implementing_Load_Balancers_with_Nginx/assets/139593350/81004c85-2085-43c5-b36c-2aa8c1620860)

4. Restart Apache2 to load the new configuration using the command below:

*sudo systemctl restart apache2*

![restart server](https://github.com/Ukdav/Implementing_Load_Balancers_with_Nginx/assets/139593350/7347c50a-480d-4c97-bfd5-e456454fb793)

![restart server 2](https://github.com/Ukdav/Implementing_Load_Balancers_with_Nginx/assets/139593350/691a90d3-e747-402c-b41d-78ab4b68d9ff)



* Creating our new html file:
	
	1. Open a new index.html file with the command below:
		
	*sudo vi index.html*

  2. Switch VI Editor to insert mode and paste the HTML file below; before pasting the HTML file, get the public IP or your EC2 instance from AWS Management Console   
     and replace the placeholder test for IP address in the HTML file:
     
     IP Address: 16.171.240.215
     
     IP Address: 51.20.130.177

![html file](https://github.com/Ukdav/Implementing_Load_Balancers_with_Nginx/assets/139593350/53c5c920-47cd-4f21-85ad-8c1fc5287d23

![configuring public ip in apache-1](https://github.com/Ukdav/Implementing_Load_Balancers_with_Nginx/assets/139593350/20ea0eeb-bda8-4c62-ac9a-cd475e1e498e)

![configure puclic IP on Apache-2](https://github.com/Ukdav/Implementing_Load_Balancers_with_Nginx/assets/139593350/20cc34e5-f91b-41af-93b0-f8aebed74a3a)


























