CI/CD Pipeline Project to Deploy a Web Application (.war)
Prerequisites
1. Maven Server
2. SonarQube Server
3. Tomcat Server
4. GitHub
5. AWS Instances for Servers
6. Jenkins Server
Project Description
This project demonstrates the deployment of a .war application using Jenkins Pipeline with Groovy script.
Previously implemented manually, Maven was set up to build the artifact, SonarQube scanned the code for
vulnerabilities, and Tomcat served as the deployment environment. The web app displays a JOMACSIT
website page.
Server Setup
1. Maven Installation (Ubuntu)
- sudo apt-get update && sudo apt-get upgrade -y
- sudo apt install openjdk-11-jdk -y
- sudo wget https://dlcdn.apache.org/maven/maven-3/3.9.9/binaries/apache-maven-3.9.9-bin.zip
- sudo unzip apache-maven-3.9.9-bin.zip
- sudo mv apache-maven-3.9.9/ /opt/maven
- Set environment variables in ~/.bash_profile
2. SonarQube Installation
- sudo apt install openjdk-11-jdk unzip wget -y
- sudo wget https://binaries.sonarsource.com/sonarqube/sonarqube-7.8.zip
- sudo unzip sonarqube-7.8.zip
- sudo mv sonarqube-7.8 /opt/sonarqube
- sudo chown -R sonar:sonar /opt/sonarqube/
- sudo su - sonar && sh /opt/sonarqube/bin/linux-x86-64/sonar.sh start
  the image below shows sonarqube server is started
  ![Screenshot (172)](https://github.com/user-attachments/assets/f876504b-9571-434f-a760-f5e83367834f)

 
3. Tomcat Installation
- sudo apt install openjdk-11-jdk wget -y
- sudo wget https://downloads.apache.org/tomcat/tomcat-9/v9.0.98/bin/apache-tomcat-9.0.98.tar.gz
- sudo tar -xzvf apache-tomcat-9.0.98.tar.gz
- sudo mv apache-tomcat-9.0.98 /opt/tomcat9
- sudo chmod 777 -R /opt/tomcat9
- sudo sh /opt/tomcat9/bin/startup.sh
 the image below shows tomcat server ready
  ![Screenshot (170)](https://github.com/user-attachments/assets/8a7c4191-9668-4973-82dc-71f64150eaff)
![Screenshot (181)](https://github.com/user-attachments/assets/ba4c15d8-f83f-4fee-a9df-a6efc3294f39)

- 
4. Jenkins Installation
- sudo apt update
- sudo apt install openjdk-11-jre -y
- sudo apt install jenkins -y
- sudo systemctl enable jenkins
- sudo systemctl start jenkins
  ![Screenshot (173)](https://github.com/user-attachments/assets/57e242c3-8fa1-4fe5-b672-110364877fee)

  The image below shows our servers running
![Screenshot (192)](https://github.com/user-attachments/assets/c349f1dc-23e8-4b7d-bf5c-fba19185874a)


From here we need to setup our Jenkins pipeline by logging into the Jenkins console. Setup your log in credentilas for Jenkins. In Jenkins console dashboard click on new item, the write name of project and select pipeline. In the next page, we can give a simple description to our project

In configuring Jenkins,we will select discard old builds,and insert 2 days
 Scroll to source code management section and select git, 
Insert our git url… which will be copied from github
 We can add our git credentials

Next is build triggers section,we select Github hook trigger for GITscm
 Under BUILD Steps click on add buildstep and select “invoke top level maven targets”

We need to download some plugins, so we click on dashboard, click on plugins, select available plugins and search for and install blue ocean to give us better view of our build, also download and  install a pluggin called safe restart 


Scripted Groovy Script:
//Scripted Groovy

node(""){
 
 def MHD = tool name: "maven3.9.9"
  
  stage("1. Git clone from repo"){
     sh "echo start of git clone"
     git branch: 'main', url: 'https://github.com/Mat-94/web-app.git'
     sh "echo end of git clone"
   }
   
   stage("2. Build from Maven"){
       sh "echo start building from maven"
       sh "${MHD}/bin/mvn clean package"
       sh "echo end of building from maven"
   }
   
   stage("3. Code Quality Scan"){
       sh "echo start code scan"
       sh "${MHD}/bin/mvn sonar:sonar"
       sh "echo end of code scan"
   }
   }

   
   stage("4. Deploy to Tomcat"){
       sh "echo start deploying to Tomcat server in UAT"
       deploy adapters: [tomcat9(credentialsId: 'tomcat_cred', path: '', url: 'http://3.15.1.85:9090')], contextPath: null, war: 'target/*.war'
       sh "echo end of deploy to Tomcat server in UAT"
   }
       
}

below is the console output of our build
![Screenshot (193)](https://github.com/user-attachments/assets/66d1a5a9-a724-49fe-ad18-caa8230d7999) 
![Screenshot (194)](https://github.com/user-attachments/assets/f0abd0d7-0d7d-4f63-84ca-f8f83f69292a)

from the image below our , sonarqube scanned the code with zero vulnerabilities and bugs
![Screenshot (187)](https://github.com/user-attachments/assets/06469a27-f788-4cdb-971a-bf69c210b6bc)
the image below also shows that our artefact was deployed to tomcat
![Screenshot (189)](https://github.com/user-attachments/assets/afe806d3-791b-4840-955c-b32a957a51a0) 
when we try accessing the webapp deployed , we will see the website page of JOMACSIT below
![Screenshot (190)](https://github.com/user-attachments/assets/6ddd6089-24fc-441b-815e-a73aa36ee793)




       
	      Challenges Encountered
	      - Java version compatibility issues resolved by upgrading Jenkins dependencies.
	      - Duplicate code in pom.xml resolved by careful error log analysis.
	      Conclusion
	      This project successfully automated the build, code quality scanning, and deployment processes
	      of a .war application using a Jenkins pipeline.
