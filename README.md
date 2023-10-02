06-09-2023

JENKINS:
its a ci/cd tool.

ci : continous integration : continous build + continous test 
old code with new code
before ci we have time waste and every thing is manual work.
after ci everything is automated.

cd : continous delivery	  : deployment manually to prod env
cd : continous deployment : deployment automatically to prod env

PIPELINE:
STEP BY STEP EXECUTION OF A PARTICULAR PROCESS.
SERIES OF EVENTS INTERLINKED WITH EACHOTHER.

CODE -- > BUILD -- > TEST -- >  DEPLOY

ENV:
DEV	: DEVELOPERS
QA	: TESTERS
UAT	: CLIENT

THE ABOVE ENVS ARE CALLED AS PRE-PROD OR NON-PROD

PROD	: USER
PROD ENV IS ALSO CALLED AS LIVE ENV


JENKINS:
its a free and opensource tool.
its platform independent.
it is built on java-11.
koshuke kawaguchui invented jenkins in sun micro systems 2004.
inital name was hudson -- > paid -- > orcle -- > free 
it consist lot of plugins.
port number for jenkins is 8080.

SETUP: CREATE EC2 WITH ALL TRAFFIC (8080)

#STEP-1: INSTALLING GIT JAVA-1.8.0 MAVEN 
yum install git java-1.8.0-openjdk maven -y

#STEP-2: GETTING THE REPO (jenkins.io --> download -- > redhat)
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

#STEP-3: DOWNLOAD JAVA11 AND JENKINS
amazon-linux-extras install java-openjdk11 -y
yum install jenkins -y
update-alternatives --config java

#STEP-4: RESTARTING JENKINS (when we download service it will on stopped state)
systemctl start jenkins.service
systemctl status jenkins.service

copy public ip and paste on browser like this
public-ip:8080
cat /var/lib/jenkins/secrets/initialAdminPassword
install plugins and create user for login.

INTEGRATION OF GIT AND MAVEN WITH JENKINS:
NEW ITEM -- > NAME: NETFLIX JOB -- > FREE STYLE -- > OK 
Source Code Management -- > GIT -- > https://github.com/devopsbyraham/jenkins-java-project.git
Build Steps -- > add setp -- > Execute shell -- > save

================================================================================================================================================
07-09-2023:



PARAMETERS: used to pass input for the jobs
choice: when we have multiple options we can select one

create a ci job -- > Configure -- > This project is parameterized -- > choice -- > name: env choice: dev,test,uat, prod -- > save

string: to pass multiple inputs 
multi line string: to pass input on multiple lines
bool: ture of false
file: to build files


PORT CHANGING:
vim /usr/lib/systemd/system/jenkins.service
67 (8080=8090)
systemctl daemon-reload
systemctl restart jenkins.service
systemctl status jenkins.service


PASSWORDLESS LOGIN:
vim /var/lib/jenkins/config.xml
line 7 (true=false)
systemctl restart jenkins.service
systemctl status jenkins.service


CHANGING BUILD LIMITS:
dashboard -- > manage jenkins -- > nodes -- > built-in node -- > configure -- > number of executors -- > 3 -- > save


HOW TO RESLOVE ISSUE WHEN JENKINS SERVER IS CRASHED: 
when we stop and start server your ip will change and the services inside server also stop.



RESTORING DELETE JOBS:

Dashboard -- > Manage Jenkins -- > Plugins -- > available plugins -- > Job Configuration History -- > install -- > go back to top page

now try to delete a job

HISTORY:
31  vim jenkins.sh
   32  sh jenkins.sh 
   33  cat /var/lib/jenkins/secrets/initialAdminPassword
   34  cd /var/lib/jenkins/workspace/netflix/
   35  ll
   36  ls target/
   37  vim /usr/lib/systemd/system/jenkins.service 
   38  system daemon-reload
   39  systemctl daemon-reload
   40  systemctl restart jenkins.service 
   41  systemctl status jenkins.service 
   42  vim /usr/lib/systemd/system/jenkins.service 
   43  systemctl daemon-reload
   44  systemctl restart jenkins.service 
   45  systemctl status jenkins.service 
   46  vim /var/lib/jenkins/config.xml 
   47  systemctl restart jenkins.service 
   48  systemctl status jenkins.service 
   49  systemctl restart jenkins.service 
   50  systemctl status jenkins.service 
   51  vim /var/lib/jenkins/config.xml 
   52  systemctl restart jenkins.service 
   53  systemctl status jenkins.service 
   54  history
=================================================================

CRONJOB: used to schedule the works on jenkins.
cron synatx is used to work with cron jobs.
cron synatx: * * * * *
each star will have space in b/w
NOTE: use server time (utc)

*	: minutes
*	: hours
*	: date
*	: month
*	: day (0=sun 1=mon ----)

11:05 08-09-2023

5 11 8 9 5
5 11 8 9 5

4:45 pm 09-09-2023

45 16 9 9 6

38 5 8 9 5

CREATE A CI JOB: Build Triggers -- > Build periodically -- > * * * * * -- > save

Limitations:
will not check the code is changed or not.

POLLSCM: it will build when code is changed only.
Note: need to set the time limit.

CREATE A CI JOB: Build Triggers -- > Poll Scm -- > * * * * * -- > save


limitation:
9am -- > 7 am : time waste 

Webhooks: it will trigger the build the moment we commit the code

github -- > repo -- > settings -- > webhooks -- > add webhook -- >
Payload URL: http://13.39.14.187:8080/github-webhook/ -- > Content type: application/json -- > Add webhook

Jenkins Dashboard -- > create ci job -- > Build triggers -- > GitHub hook trigger for  GITScm polling -- > save  


THROTTLE BUILD: to restrict number of builds per interval.
create job -- > Throttle builds -- > Number of build: 3 -- > time period: hours -- > save 

make builds and test them

=================================================================

MASTER AND SLAVE:
it is used to distribute the builds.
it reduce the load on jenkins server.
communication blw master and slave is ssh.
Here we need to install agent (java-11).
slave can use any platform.
label = way of assingning work for slave.

SETUP:
#STEP-1 : Create a server and install java-11
amazon-linux-extras install java-openjdk11 -y

#STEP-2: SETUP THE SLAVE SERVER
Dashboard -- > Manage Jenkins -- > Nodes & Clouds -- > New node -- > nodename: abc -- > permanaent agent -- > save 

CONFIGURATION OF SALVE:

Number of executors : 3 #Number of Parallel builds
Remote root directory : /tmp #The place where our output is stored on slave sever.
Labels : swiggy #place the op in a particular slave
useage: last option
Launch method : last option 
Host: (your privte ip)
Credentials -- > add -- >jenkins -- > Kind : ssh username with privatekey -- > username: ec2-user 
privatekey : pemfile of server -- > save -- > 
Host Key Verification Strategy: last option

DASHBOARD -- > JOB -- > CONFIGURE -- > RESTRTICT WHERE THIS JOB RUN -- > LABEL: SLAVE1 -- > SAVE



LINKED JOBS: 
here jobs are linked with each other.
types:
1. Up stream
2. Down stream

create a job2
job1 -- > configuration -- > post build actions -- > build other projects -- > name of job2 -- > save

=========================================================================
PIPELINE: 
stpe by step execution of particular process
series of events interlinekd with each other
pipelines are sytnax-based
we use groovy language to write pipleine.
its a DSL (domain specific language)
it gives the vizualization of a process execution.


CODE -- > BUILD -- > TEST -- > ARTIFACT -- > DEPLOY

TYPES:
1. SCRIPTED 
2. DECLARATIVE

DECLARATIVE PIPELINE:

SINGLE STAGE:

pipeline {
    agent any
    
    stages{
        stage('abc') {
            steps {
                sh 'touch file2'
            }
        }
    }
}

MULTI STAGE:

pipeline {
    agent any
    
    stages{
        stage('abc') {
            steps {
                sh 'touch file1'
            }
        }
	 stage('abc') {
            steps {
                sh 'touch file2'
            }
        }
    }
}

NOTE: PASSS 
P : PIPELINE
A : AGNET
S : STAGES
S : STAGE
S : STEPS


CI PIPELINE:

pipeline {
    agent any 
    
    stages{
        stage('checkout') {
            steps {
                git 'https://github.com/devopsbyraham/jenkins-java-project.git'
            }
        }
        stage('build') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('artifact') {
            steps {
                sh 'mvn clean package'
            }
        }
    }
}


PIPELINE AS A CODE:
executing multiple commands or multiple actions on a single stage.

pipeline {
    agent any
    
    stages {
        stage('one') {
            steps {
                git 'https://github.com/devopsbyraham/jenkins-java-project.git'
                sh 'mvn compile'
                sh 'mvn test'
                sh 'mvn clean package'
            }
        }
    }
}

PIPELINE AS A CODE OVER MULTI STAGE:
executing multiple commands or multiple actions on a multiple stage.


pipeline {
    agent any
    
    stages {
        stage('one') {
            steps {
                git 'https://github.com/devopsbyraham/jenkins-java-project.git'
                sh 'mvn compile'
            }
        }
        stage('two') {
            steps {
                sh 'mvn test'
                sh 'mvn clean package'
            }
        }
    }
}

PIPELINE AS A CODE OVER SINGLE-SHELL:
executing multiple commands over a single shell.


pipeline {
    agent any
    
    stages {
        stage('one') {
            steps {
                git 'https://github.com/devopsbyraham/jenkins-java-project.git'
                sh '''
                mvn compile
                mvn test
                mvn package
                mvn install
                mvn clean package
                '''
            }
        }
    }
}



SONARQUBE:
its a tool used to check the code quality.
it covers the bugs, vulnerabilities, code duplications, code smells.
its supports 20+ programming langauges.
we have both free and paid version.

port: 9000
req: t2 medium
dependency: java-11

SETUP:

#! /bin/bash
#Launch an instance with 9000 and t2.medium
cd /opt/
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.9.6.50800.zip
unzip sonarqube-8.9.6.50800.zip
amazon-linux-extras install java-openjdk11 -y
useradd sonar
chown sonar:sonar sonarqube-8.9.6.50800 -R
chmod 777 sonarqube-8.9.6.50800 -R
su - sonar

#run this on server manually
#sh /opt/sonarqube-8.9.6.50800/bin/linux/sonar.sh start
#echo "user=admin & password=admin"

after login -- > add project -- > manual -- > project key -- > netlfix -- > setup -- > token -- > netflix -- > generate -- > continue -- > maven 

copy paste the code on pipeline:

pipeline {
    agent any 
    
    stages{
        stage('checkout') {
            steps {
                git 'https://github.com/devopsbyraham/jenkins-java-project.git'
            }
        }
        stage('build') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('code quality') {
            steps {
                sh '''
                mvn sonar:sonar \
                  -Dsonar.projectKey=netflix \
                  -Dsonar.host.url=http://13.38.32.159:9000 \
                  -Dsonar.login=3f017506d83cd334fba02ecf9db706c429ab5d38
                '''
            }
        }
        stage('artifact') {
            steps {
                sh 'mvn clean package'
            }
        }
    }
}

TO EXCECUTE ON SLAVE

pipeline {
    agent {
        label 'slave1'
    } 
    
===================================================================================
12-09-2023:

VARIABLES:
assigning the value 

a=10
name=raham

1. USER DEFINED VARIABLES:
a. local vars: we can define inside job
2. global vars: we can define outside jobs

2. JENKINS ENV VARS:
their vars are going to change as per build.

HOW TO PASS VARIABLES ON PIPELINE:


pipeline {
    agent any 
    
    environment {
        name = "raham"
        loc = "hyderabad"
    }
    stages {
        stage('one') {
            steps {
                echo "hai all my name is $name and im from $loc"
                sh 'env'
            }
        }
    }
}

=====================================================================

POST BUILD ACTIONS:
actions the we perform after the build is called as post build action.

always: if build failed or success it will run the post actions.
success: it will execute post actions only when the build is successful.
failure: it will execute post actions only when the build is failed.

pipeline {
    agent any 
    
    environment {
        name = "raham"
        loc = "hyderabad"
    }
    stages {
        stage('one') {
            steps {
                s 'env'
            }
        }
    }
    post {
        always {
            echo "the build is done"
        }
    }
}

===============================================================================================

RBAC: ROLE BASE ACCESS CONTROL

in real time to restrict the access for jenkins console we use rbac.
with rbac we can give set of limited permissions for user.

1. user:
dashboard -- > manage jenkins -- > users -- > create user (ramesh, suresh)

2. Plugin: 
Dashboard -- > Manage Jenkins -- >Plugins -- >available plugin  -- >Role-based Authorization Strategy -- >install

3. configure:
Dashboard -- > Manage Jenkins -- >security -- > security -- > Authorization -- > Role-based Authorization -- > save

4. manage and assign roles

Dashboard -- > Manage Jenkins -- >Manage and Assign Roles -- > Manage Role
role1: fresher -- > read
role2: exp -- > admin

ASSIGN ROLES

--------------------------------------------------------------

TOMCAT SETUP:
1. wget http://dlcdn.apache.org/tomcat/tomcat-9/v9.0.80/bin/apache-tomcat-9.0.80.tar.gz
2. tar -zxvf apache-tomcat-9.0.80.tar.gz
3. vim apache-tomcat-9.0.80/conf/tomcat-users.xml

  <role rolename="manager-gui"/>
  <role rolename="manager-script"/>
  <user username="tomcat" password="raham123" roles="manager-gui, manager-script"/>

4. vim apache-tomcat-9.0.80/webapps/manager/META-INF/context.xml (delete 21,22)
5. sh apache-tomcat-9.0.80/bin/startup.sh

public-ip slave:8080
manager app -- > usrername and passowd

Download pluguin -- > Deploy to container : this is to deploy on tomcat


Dashboard
Manage Jenkins
Credentials
System
Global credentials (unrestricted)
add credentials -- > username and password -- > save

http://13.39.49.204:8080/netflix/

TROUBLESHOOTING TECHNIQUES:
1. SERVER LEVEL
2. JOB LEVEL
3. CODE LEVEL

1. SERVER LEVEL:
A. CONSOLE OP
B. MAVEN & JAVA
C. DEPENDENCY MISTAKE 
D. CPU & MEMEORY

2. JOB LEVEL:
A. REPO AND BRANCH CONFIG
B. SYNTAX
C. PLUGINS


vim /root/apache-tomcat-9.0.80/conf/server.xml
sh /root/apache-tomcat-9.0.80/bin/startup.sh
sh /root/apache-tomcat-9.0.80/bin/shutdown.sh

pipeline {
    agent {
        label 'slave1'
    }
    
    stages {
        stage('checkout') {
            steps {
                git 'https://github.com/devopsbyraham/jenkins-java-project.git'
            }
        }
        stage('build') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('code quality') {
            steps {
                sh '''
                   mvn sonar:sonar \
                     -Dsonar.projectKey=project-netflix \
                     -Dsonar.host.url=http://52.47.193.26:9000 \
                     -Dsonar.login=54f6a0567ef5a12acda5eecee7ce51e0feb16bb1
                '''
            }
        }
        stage('artifact') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('deploy') {
            steps {
                deploy adapters:[
                    tomcat9(
                        credentialsId: 'a3f384b0-0de7-4b62-9800-24043d1d135d',
                        path: '',
                        url: 'http://13.39.49.204:8080/'
                        )
                    ],
                    contextPath: 'netflix',
                    war: 'target/*.war'
            }
        }
    }
}# jenkins
