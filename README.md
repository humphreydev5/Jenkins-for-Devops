# Jenkins Setup with Docker, CI/CD, and Kubernetes Deployment

## Overview

This guide walks you through the steps to:

1. Install Jenkins on an AWS EC2 instance.
2. Configure Docker as an agent.
3. Set up a CI/CD pipeline.
4. Deploy applications to Kubernetes (K8s) using Argo CD in a GitOps manner.

---

## Prerequisites

1. AWS Account and an EC2 instance running Ubuntu.
2. Basic knowledge of AWS, Jenkins, Docker, and Kubernetes.

---

## Step 1: Set Up AWS EC2 Instance

1. Log in to your [AWS Console](https://aws.amazon.com/).
2. Navigate to **Instances (running)** and click **Launch Instances**.
3. Follow the prompts to configure and launch your instance.

<img width="994" alt="Screenshot 2023-02-01 at 12 37 45 PM" src="https://user-images.githubusercontent.com/43399466/215974891-196abfe9-ace0-407b-abd2-adcffe218e3f.png">

---

## Step 2: Install Jenkins

### 2.1 Pre-Requisites

- Ensure Java (JDK) is installed.

Run the following commands to install Java:

```bash
sudo apt update
sudo apt install openjdk-17-jre
```

Verify Java is Installed

```
java -version
```

### 2.2 Install Jenkins

Run the following commands to install Jenkins:

```
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

### 2.3 Allow Inbound Traffic to Jenkins

By default, Jenkins is not accessible externally. Open port 8080 in your EC2 instance's security group:

1. Navigate to EC2 > Instances > Click on your instance.

2. Scroll to the Security tab and click Security groups.

3. Edit the inbound traffic rules to allow TCP traffic on port 8080 (or allow all traffic if preferred).

<img width="1187" alt="Screenshot 2023-02-01 at 12 42 01 PM" src="https://user-images.githubusercontent.com/43399466/215975712-2fc569cb-9d76-49b4-9345-d8b62187aa22.png">


### 2.4 Access Jenkins

Access Jenkins using the public IP address of your EC2 instance:

```
http://<ec2-instance-public-ip>:8080
```

### 2.5 Unlock Jenkins

1. Run the following command to retrieve the Jenkins Admin Password:

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

2. Enter the password on the Jenkins setup page.

Note: If you are not interested in allowing `All Traffic` to your EC2 instance
      1. Delete the inbound traffic rule for your instance
      2. Edit the inbound traffic rule to only allow custom TCP port `8080`
  
After you login to Jenkins, 
      - Run the command to copy the Jenkins Admin Password - `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`
      - Enter the Administrator password
      
<img width="1291" alt="Screenshot 2023-02-01 at 10 56 25 AM" src="https://user-images.githubusercontent.com/43399466/215959008-3ebca431-1f14-4d81-9f12-6bb232bfbee3.png">

### 2.6 Complete Setup

Click Install suggested plugins.

<img width="1291" alt="Screenshot 2023-02-01 at 10 58 40 AM" src="https://user-images.githubusercontent.com/43399466/215959294-047eadef-7e64-4795-bd3b-b1efb0375988.png">

Wait for the plugins to install.

<img width="1291" alt="Screenshot 2023-02-01 at 10 59 31 AM" src="https://user-images.githubusercontent.com/43399466/215959398-344b5721-28ec-47a5-8908-b698e435608d.png">

Create an admin user or skip to proceed with the default setup.
[If you want to use this Jenkins instance for future use-cases as well, better to create admin user]

<img width="990" alt="Screenshot 2023-02-01 at 11 02 09 AM" src="https://user-images.githubusercontent.com/43399466/215959757-403246c8-e739-4103-9265-6bdab418013e.png">

Jenkins is now ready for use!

<img width="990" alt="Screenshot 2023-02-01 at 11 14 13 AM" src="https://user-images.githubusercontent.com/43399466/215961440-3f13f82b-61a2-4117-88bc-0da265a67fa7.png">

## Install the Docker Pipeline plugin in Jenkins:

   - Log in to Jenkins.
   - Go to Manage Jenkins > Manage Plugins.
   - In the Available tab, search for "Docker Pipeline".
   - Select the plugin and click the Install button.
   - Restart Jenkins after the plugin is installed.
   
<img width="1392" alt="Screenshot 2023-02-01 at 12 17 02 PM" src="https://user-images.githubusercontent.com/43399466/215973898-7c366525-15db-4876-bd71-49522ecb267d.png">

Wait for the Jenkins to be restarted.


## Docker Slave Configuration

Run the below command to Install Docker

```
sudo apt update
sudo apt install docker.io
```
 
### Grant Jenkins user and Ubuntu user permission to docker deamon.

```
sudo su - 
usermod -aG docker jenkins
usermod -aG docker ubuntu
systemctl restart docker
```

Once you are done with the above steps, it is better to restart Jenkins.

```
http://<ec2-instance-public-ip>:8080/restart
```

The docker agent configuration is now successful.




