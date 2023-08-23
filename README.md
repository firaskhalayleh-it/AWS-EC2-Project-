# Deploying a Book Management App on AWS with Auto-Scaling and Systemd

In this guide, we will walk through the process of deploying a Book Management app, which is a REST API built using Express.js, to AWS. We will deploy the app in an Auto-Scaling Group behind an Application Load Balancer and manage it using systemd. Additionally, we will include an endpoint that returns the EC2 instance's IP address for testing purposes.

## Prerequisites

Before you begin, you'll need:

1. An AWS account.
2. Basic knowledge of AWS services like EC2, Auto Scaling, and ALB.
3. Node.js and npm installed locally.
4. Git installed locally.
5. Your Book Management app's source code.

## Step 1: Prepare the App

1. Clone your Book Management app repository if you haven't already.
2. Build and package the app:
   
   ```sh
   tar czvf app.tar.gz package.json package-lock.json dist/*
   ```
   
   This will create a compressed package containing the necessary files.

3. Upload the `app.tar.gz` to a public GitHub release in your repository.

## Step 2: Launch EC2 Instances

1. Go to the AWS Management Console.
2. Launch an EC2 instance using an Amazon Machine Image (AMI) that suits your requirements. Make sure it's based on a Linux distribution.
3. Choose an instance type and configure other settings as needed.
4. In "Configure Security Group," allow incoming traffic on the required ports for your app (e.g., port 80 for HTTP).
5. Launch the instance and create a new key pair or use an existing one to access the instance via SSH.

## Step 3: Setup Auto Scaling Group and ALB

1. Go to the EC2 Dashboard.
2. Click on "Auto Scaling Groups" in the left menu and then click "Create Auto Scaling group."
3. Choose the instance that you launched earlier.
4. Configure the scaling options, such as the desired and maximum capacity.
5. Configure the health checks and scaling policies. For the CPU utilization scaling policy, set the target value to 60.
6. Complete the wizard, and the Auto Scaling Group will ensure that the desired capacity of instances is running.

7. Next, go to the EC2 Dashboard and click on "Load Balancers" in the left menu.
8. Create a new Application Load Balancer (ALB).
9. Configure the listeners and routing based on your app's requirements.
10. In "Configure Security Groups," select the security group that allows traffic on the app's port.
11. Complete the wizard to create the ALB.

## Step 4: Systemd Service

1. SSH into the EC2 instance using the key pair you generated.
   
   ```sh
   ssh -i /path/to/your/key.pem ec2-user@instance-ip
   ```

2. Create a systemd service unit file for your app:
   
   ```sh
   sudo nano /etc/systemd/system/book-management-app.service
   ```

3. Add the following content to the file, adjusting paths as necessary:

```plaintext

   [Unit]
    Description=Simple App Service
    After=syslog.target
After=network.target

[Service]
AmbientCapabilities=CAP_NET_BIND_SERVICE
Type=simple
User=ubuntu
Group=ubuntu
WorkingDirectory=/home/ubuntu/app
ExecStart=/usr/bin/node ./dist/index.js
Restart=always
Environment=PORT=80

[Install]
WantedBy=multi-user.target
  ``` 
  
4. Save and exit the text editor.
5. Reload systemd and start the service:
   
   ```sh
   sudo systemctl daemon-reload
   sudo systemctl start app
   ```

6. Enable the service to start on boot:
   
   ```sh
   sudo systemctl enable pp
   ```

## Step 5: Test the App

1. Visit the ALB's DNS name or IP address in your web browser. You should see your Book Management app running.
2. Test the EC2 instance IP address endpoint to ensure that instances are behind the ALB:
   
   ```sh
   curl http://your-alb-dns-or-ip/ec2-ip
   ```

## Step 6: Cleanup

Remember to terminate all the resources when you're done to avoid unnecessary charges.

## Conclusion

Congratulations! You've successfully deployed your Book Management app on AWS using an Auto-Scaling Group, an Application Load Balancer, and managed it using systemd. This setup ensures high availability and scalability for your application. Feel free to share your deployment process with others by creating a blog post or article.


![Screenshot from 2023-08-22 21-48-14](https://github.com/firaskhalayleh-it/AWS-EC2-Project-/assets/91515883/810558dc-a9c5-4a36-9933-edb6e888f3c5)


![Screenshot from 2023-08-23 09-25-24](https://github.com/firaskhalayleh-it/AWS-EC2-Project-/assets/91515883/dc80c21a-3c65-4394-91f8-c2e0a14744d2)

![Screenshot from 2023-08-23 09-25-47](https://github.com/firaskhalayleh-it/AWS-EC2-Project-/assets/91515883/59305d75-7b10-4ae3-8a01-9ef68c69465b)


![Screenshot from 2023-08-23 09-26-05](https://github.com/firaskhalayleh-it/AWS-EC2-Project-/assets/91515883/775d9a67-4beb-4df5-9a4a-1b9a57d77270)


![Screenshot from 2023-08-23 09-26-16](https://github.com/firaskhalayleh-it/AWS-EC2-Project-/assets/91515883/8eae0a53-61d4-4c6c-8d4a-02032d887be1)

![Screenshot from 2023-08-23 09-26-30](https://github.com/firaskhalayleh-it/AWS-EC2-Project-/assets/91515883/0f2e738a-828d-4301-b05d-034600975763)

![Screenshot from 2023-08-23 09-26-49](https://github.com/firaskhalayleh-it/AWS-EC2-Project-/assets/91515883/d7d954af-b358-462b-9cfd-fa259fcaba9e)

![Screenshot from 2023-08-23 09-27-04](https://github.com/firaskhalayleh-it/AWS-EC2-Project-/assets/91515883/f2e565ad-dbb5-4c4a-a957-e5490bfb4d66)

![Screenshot from 2023-08-23 09-27-21](https://github.com/firaskhalayleh-it/AWS-EC2-Project-/assets/91515883/24067936-6978-4629-b864-420116bbcf43)


![Screenshot from 2023-08-23 09-27-36](https://github.com/firaskhalayleh-it/AWS-EC2-Project-/assets/91515883/c9d6c642-7c4b-41b4-bdad-215009912c0b)

![Screenshot from 2023-08-23 09-28-43](https://github.com/firaskhalayleh-it/AWS-EC2-Project-/assets/91515883/58fa9aff-6f11-41fc-a6cb-8dde227a2eb2)
