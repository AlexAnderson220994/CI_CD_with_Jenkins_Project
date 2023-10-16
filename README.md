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

1) Go to the EC2 Console on AWS.
2) In the left hand pane on the console, click on `Instances`, then make a new instance

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

````
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
````

## Building the CI/CD Pipeline in Jenkins

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

### 1) Setting up CI job for 'dev' branch

1) On the Configuration settings for the CI job(job 1)
2) Go to "Source code management":
- Under "Branches to build", change this from `*/main` to `*/dev`
![Alt text](<images/Git Merge/Screenshot 2023-10-12 113240.jpg>)
3) Under "Post build actions"
- Choose your merge job (Heading 2)
- Press `Trigger only if build is stable`
![Alt text](<images/Git Merge/Screenshot 2023-10-12 113339.jpg>)

### 2) Setting up Merge job

![Alt text](<images/Git Merge/Screenshot 2023-10-12 113459.jpg>)
![Alt text](<images/Git Merge/Screenshot 2023-10-12 113550.jpg>)
![Alt text](<images/Git Merge/Screenshot 2023-10-12 113637.jpg>)
![Alt text](<images/Git Merge/Screenshot 2023-10-12 113708.jpg>)

### 3) Change repo from main to dev on Gitbash

1) `cd` into the folder for the Repo you're working with
2) Input the command `git branch dev` to create a dev branch within this repo.
3) Change to the dev branch from the main branch with the command `git checkout dev`.
4) The command from point 3 also works to change back to main.

### 4) Push from dev branch

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