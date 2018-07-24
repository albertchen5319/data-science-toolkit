# data-science-toolkit

1. SSH Keys

SSH Keys are a way to authenticate into another system without using a password.  
You create two files on your system. id_rsa(private key) and id_rsa.pub(public key)  
This is known as a key pair.  
The public key acts as a lock on your home's front door, and the private key acts as the key to connect.   

2. Amazon EC 2

EC2, or elastic computer cloud, allows us to make servers.  
S3, simple storage service, allows us to store things.  
We need to first setup our SSH on EC2.  
Go to resources -> key pairs -> import key pair.  
Give it a name and load in the public key from our file.  

3. Security Groups

Next, we need to set up our security groups.  
The internet uses ports to direct traffic.  
Default port is 80. SSH is port 22. HTTPS is port 443.  
Amazon works on a principle of least possible privilege.  
We are going to open up a certain set of ports to use for web traffic on our machines.  
Create new security group. Name it. Add a description.  
We're concerned about inbound traffic, so we need to add some inbound rules.  
We need to be able to remote loging using SSH, otherwise we can't work with our system.  
Add SSH port 22, HTTP port 80, HTTPS port 443, PostgreSQL port 5432, and Custom TCP port 27016.  
For all of these rules, choose source anywhere, which means anyone can access these ports.  
27017 is the default mongo port, but they do not have security so we will use 27016.  
We will run Jupyter on 443.  
Finish configuring the security group by clicking create.  

4. AWS Operating System

Now we head to the EC2 dashboard. We should see 0 running instances.  
There are essentially large server farms that Amazon manages, and we will get a very small percentage of it for our machine.  
Click launch instance. AMI is amazon machine image.  Even numbers for ubuntu versions are stable.  
We will use ubuntu server 16.04, which should be free tier eligible. Click select.  
Choose free tier eligible T2 micro "for our virtual hardware." This gives us 1 CPU and 1 GB of memory.  
T and M are for general purpose. P is for unique. C is computer optimized. M is optimized.  
Next, configure instance details. No need to change anything. Note Spot instances.  
Then add storage. 20 GB should be sufficient.  Skip tags.  
Now choose an existing security group, not default. Choose the one we configured earlier.  
Then review all of our configurations, and click launch.  
Choose our existed key pair from above. Make sure to check the box for I acknowledge.  Launch instance.  
Go to the instances page and give your server a name.  
Copy the public IPv4 public address.  
Go to bash and type: ssh ubuntu@<ip>  
Then you should see ubuntu and your ip to verify you successfully connected.  

5. Docker Installation

Next, we will install docker. This next step is generally unsafe, but docker is extremely trusted.  
curl -sSL https://get.docker.com | sh  
This means run the script that gets returned from the URL.  
Then to give ubuntu system level access to docker. We need to enter in the given command  
sudo usermod -aG docker ubuntu  
Now we need to disconnect and reconnect, using ctrl-d.  
Reconnect to our ubuntu server, and type tmux.  

6. Obtaining the correct Docker image

We use tmux for a more stable connection.  
In tmux, we will now pull the docker jupyter data science image  
docker pull jupyter/datascience-notebook  
docker run -d -p 443:8888 -v /home/ubuntu:/home/jovyan jupyter/datascience-notebook  
-d is detached, allows us to close the shell without shutting down our jupyter server  
-p is attaching the appropriate ports. the pattern is host:docker  
Docker can not be worried about the state. Containers need to be stateless.  
This immediately creates the files and it is a two way connection and immediate.  

7. Running the correct Docker image as a container

Now we need to get the token for our docker container. There are two ways.  
docker ps  
Look at the container ID, and note the first few characters.  
docker logs <containerID>  
OR  
docker exec <containerID> jupyter notebook list  
Grab the token. Then go to your browser. In the URL box, enter <IP>:443  
Paste in your container token.  
Success!  

8. Jupyter notebook security concerns



9. Anything else I may have forgotten ...



10. Create at least one diagram of your overall system.



11. A detailed budget of the costs of running a Jupyter Data Science Notebook Server for three months using at least three different kinds of EC 2 instances.

For t2 micro, it is $0.0116 per hour. So, for a month, it will be $8.35. For three months, $25.05.  
For m4 large, it is $0.10 per hour. So, for a month, it will be $72.00. For three months, $216.00.  
For c5 large, it is $0.085 per hour. So, for a month, it will be $61.20. For three months, $183.60.  
