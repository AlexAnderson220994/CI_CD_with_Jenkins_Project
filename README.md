# CI/CD - Setting up Jenkins and creating a Pipeline

## CI/CD Pipeline Diagram
![Alt text](<images/Diagram v2.jpg>)

## Creating a Jenkins Environment Diagram

![Alt text](<images/Jenkins Env Diagram.jpg>)

## Creating a VPC to run Jenkins

1) Go to the VPC Console on AWS.
2) Create a VPC in the following order:
- Create VPC
- Create PUBLIC subnet
- Create Internet Gateway and link to the VPC
- Create Public Route Table

## Creating a Jenkins Environment

### Make the Instance for Jenkins

1) Go to the EC2 Console on AWS.
2) In the left hand pane on the console, click on `Instances`, then make a new instance.
3) Give the Instance a name e.g. `tech254-alex-jenkins-server`.
4) Select Ubuntu 20.04 lts (free tier eligible) as the AMI.
5) Choose the SSH key pair.
6) Select the VPC you made for Jenkins and select `Public Subnet` and choose `Enable` for the Public IP.
7) Make a Security Group that allows for:
- Port 22 (SSH)
- Port 80 (HTTP)
- Port 8080 (Jenkins)
![Alt text](<images/2_creating_jenkins_env/1. SG.jpg>)
8) Click `Launch Instance`

### Install the dependencies

9) SSH into your instance using GitBash
10) Do `sudo apt update` and `sudo apt upgrade -y`.
11) Run the following commands to install the correct versions of Java and Jenkins:
````
# Update the package manager
sudo apt update

# Install Java
sudo apt install openjdk-11-jdk

# Add the Jenkins repository
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -

sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'

# Input this command
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 5BA31D57EF5975CA

# Update the package manager again
sudo apt update

# Install Jenkins
sudo apt install jenkins

# Start the Jenkins service
sudo systemctl start jenkins

# Enable the Jenkins service
sudo systemctl enable jenkins
````
12) Once this has been done successfully, copy the Public IP and paste into the URL search bar on google.
13) Add `:8080` at the end to access the Jenkins Port.
14) Do the command below on your Jenkins instance in GitBash to get the password to log in.
````
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
````
15) On "Customise Jenkins", either choose "Install Suggested" OR "Select plugins"
![Alt text](<images/2_creating_jenkins_env/2. customise Jenkins.jpg>)
16) If you choose "Select", make sure to select office connector and NodeJs as a must.
17) Either log in as admin or make a profile to log in with a new password.
![Alt text](<images/2_creating_jenkins_env/3. jenkins homepage.jpg>)
18) You are now on the Jenkins Homepage

## Building the CI/CD Pipeline in Jenkins

### 1) Make a Webhook on GitHub

1) Go to your GitHub Profile.
2) Select the repo containing the app folder.
3) Go to settings on that repo.
4) On the left hand pane, select `Webhooks`
5) Select `Add webhook`
6) Input the Public URL of the Jenkins with `/github-webhook/` added to the end.

### 1) Creating a job

1) To create a new job on Jenkins:
- In the left hand pane, click on `New item`.
- Enter an item name for the job you're (something relatable) e.g. `alex-1-CI`.
- Choose the project type, in this case `Freestyle project`.
- Click `OK`.
2) Add a description under the "General" tab.
3) Under "Office 365 Connector"
- Restrict where the project can be run.
5) Under "Source code management", select `Git`
6) In "repo URL", paste in your SSH URL for your Github Repo
4)  Also in "General", limit the number of previous builds that can be kept so you don't end up with loads of jobs and crash the system
- Scroll down until you're in the "Build" tab and run commands to test out what the OS environment is and if Jenkins can run it.
ssh-keygen -t rsa -b 4096 -C your.email@here.comgit
5) Go to "Source code management":
- Under "Branches to build", change this from `*/main` to `*/dev`
6) Under "Post build actions"
- Choose your merge job (Heading 2)
- Press `Trigger only if build is stable`

### 2) Change repo from main to dev on Gitbash

1) `cd` into the folder for the Repo you're working with
2) Input the command `git branch dev` to create a dev branch within this repo.
3) Change to the dev branch from the main branch with the command `git checkout dev`.
4) The command from point 3 also works to change back to main.

### 3) Push from dev branch

1) Make sure you're on the 'dev' branch on your gitbash terminal.
2) Do the `git add` and `git commit` for any files you've made changes to.
3) Push to github using `git push -u origin dev`.
4) Check Jenkins to see if there are any errors.

## Blockers Encountered

### Adding the private SSH key for the App Repo to the Jenkins job

- If the SSH repo link doesn't recognise the private key you added, do the following commands on Gitbash when you're connected to the Jenkins Instance:
````
sudo su - jenkins
````
````
ssh-keyscan github.com >> ~/.ssh/known_hosts
````