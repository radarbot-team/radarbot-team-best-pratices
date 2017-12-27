
![Jenkins](static/jenkins.png)

---
# Jenkins

* Introduction
* Best practices
* References


---
#  Introduction
Jenkins is a Continuous Integration (CI) server or tool which is written in java. It provides Continuous Integration services for software development, which can be started via command line or web application server. It was originally developed as the Hudson project. Hudson’s creation started in summer of 2004 at Sun Microsystems. It was first released in java.net in february 2005. One of the advantages of Jenkins is that it's an open source tool with much support from its community.

# Best practices for using Jenkins

##### Starting from basics:
* Keep jobs simple. If a job needs to do something complex, put it in a script in GitHub/Bitbucket repos and check it out as needed.
* Archive unused jobs before removing them.
* Tag, label, or baseline the codebase after the successful build.

##### Job/branch management
* Setup a different job/project for each maintenance or development branch you create

##### Naming convention
* Specify the environment (dev/test/pro) in the name of the project or in the view's name.

##### Security
* Enable Security and Access control. Jenkins never comes with it by default. Keep the user roles to the point of your needs.
* Do not run any jobs on Master. When you run the jobs on master, you are giving free access to the Jenkins home directory to the teams running the jobs.
* Backup the Jenkins home directory regularly.

##### Optimize your resources
* To minimize the disk-space usage, clean the work-space after the build was completed successfully.
* Build one slave per project in a multiple project environment. This will help you install and manage software, based on the project requirements. This will minimize the impact between the projects. You can as well, discard or turn of the slave machine when the project is completed or the slave is not in use.

##### Reporting
* Report failure of the build job to the project owner as soon as the build job fails, using the automated emailing functionality.

# References
1. https://wiki.jenkins.io/display/JENKINS/Jenkins+Best+Practices
2. https://gist.github.com/guykisel/fdc3bb48392a4d871140
3. https://www.linkedin.com/pulse/best-practices-using-jenkins-devops-infrastructure-sharath-manjithaya
4. https://vmokshagroup.com/blog/what-is-jenkins/


