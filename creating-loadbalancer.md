<h1>Introduction:</h1>

A Load Balancer is used to distribute traffic to other servers in order to lessen the load on one single server. Requests from users can be more evenly distributed and handled efficiently. Load balancers can be hardware or software based but both use algorithms to distribute traffic effectively. Some of the algorithms that load balancers use include Round Robin, Least Conncections, IP Hash, etc. Most modern websites need multiple servers and load balancers in order to handle hundreds of thousands of user requests. Load balancers help websites output the correct information consistently to all users. In our case we will have to create 3 separate instances (VMs) with the same content and settings. 

<h3>Step 1: Enable Compute Engine API</h3>
- use command:			
 
 					gcloud services enable compute.googleapis.com

<h3>Step 2: Create VM Instances</h3>
- use commands: 
 
					gcloud compute instance-groups unmanaged create instance-group-1 --zone=your-zone
					gcloud compute instance-groups unmanaged create instance-group-2 --zone=your-zone
					gcloud compute instance-groups unmanaged create instance-group-3 --zone=your-zone

<h3>Step 3: Add Instances to Instance Groups</h3> 
- use commands:
 
					gcloud compute instance-groups unmanaged add-instances instance-group-1 --instances=instance-1,instance-2,instance-3 --zone=your-zone
	
					gcloud compute instance-groups unmanaged add-instances instance-group-2 --instances=instance-4,instance-5,instance-6 --zone=your-zone

					gcloud compute instance-groups unmanaged add-instances instance-group-3 --instances=instance-7,instance-8,instance-9 --zone=your-zone

<h3>Step 4: Create a Health Check</h3>
- use command: 
					
     					gcloud compute http-health-checks create http-basic-check

<h3>Step 5: Create a Backend Service</h3>
- use command: 
					
     					gcloud compute backend-services create web-backend-service \--protocol=HTTP --health-checks=http-basic-check --global

- add instance groups to the backend service:
- use commands: 
					
     					gcloud compute backend-services add-backend web-backend-service \--instance-group=instance-group-1 --instance-group-zone=your-zone --global

					gcloud compute backend-services add-backend web-backend-service \--instance-group=instance-group-2 --instance-group-zone=your-zone --global

					gcloud compute backend-services add-backend web-backend-service \--instance-group=instance-group-3 --instance-group-zone=your-zone --global

<h3>Step 6: Create a URL Map</h3>
- use command: 
					
     					gcloud compute url-maps create web-map \--default-service web-backend-service

<h3>Step 7: Create a Target HTTP Proxy</h3>
- A Target HTTP Proxy directs incoming HTTP or HTTPS traffic to a backend service or multiple backend services. This is important because when using multiple VM instances (backend services), the HTTP Proxy will be able to evenly spread out incoming traffic so no one server gets overwhelmed. 
- use command: 
					
     					gcloud compute target-http-proxies create http-lb-proxy \--url-map web-map

<h3>Step 8: Create a Global Forwarding Rule</h3>

    A global forwarding rule allows incoming traffic to be directed to a specific target across multiple regions. The range for global forwarding is exactly that; global. 
- use command: 

     					gcloud compute forwarding-rules create http-content-rule \--global --target-http-proxy=http-lb-proxy --ports=80

<h3>Step 9: Configure DNS</h3>
- DNS should point to the IP address of the global forwarding rule 
	
<h3>Step 10: TEST</h3>
- Test your load balancer using the configured domain or IP address. Requests should be distributed among your 3 servers. Congrats!
