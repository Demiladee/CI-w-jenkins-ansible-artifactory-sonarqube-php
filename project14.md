## setting up the different servers 

installing necessary applications and dependencies on jenkins, nginx, database and tooling servers.

```
yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
yum install python3 python3-pip wget unzip git -y
python3 -m pip install --upgrade setuptools
python3 -m pip install --upgrade pip
python3 -m pip install PyMySQL
python3 -m pip install mysql-connector-python
python3 -m pip install psycopg2==2.7.5 --ignore-installed
```

```
Installing JAVA
====================================

sudo yum install java-11-openjdk-devel -y
open the bash profile
vi .bash_profile

paste the below in the bash profile
export JAVA_HOME=(dirname (readlink $(which javac))))) export PATH=$PATH:$JAVA_HOME/bin export CLASSPATH=.:$JAVA_HOME/jre/lib:$JAVA_HOME/lib:$JAVA_HOME/lib/tools.jar

reload the bash profile
source ~/.bash_profile
```
![](images/epelrelease.png)

![](images/remirepositoryinstall.png)

![](images/gitinstall.png)

![](images/javainstall.png)

![](images/wgetb4jenkins.png)

![](images/vijava11.png)

![](images/vijavaaftjavainstall1.png)

![](images/reloadbashprofile2.png)

- installed jenkins and configured jenkins on the jenkins server

- installed and configured blue ocean on the server as well, connecting it to github so the builds can be automatically triggered

![](images/jenkinsinstall3.png)

![](images/jenkinssetup33.png)

![](images/jenkinssetup333.png)

![](images/jenkinsrepokey.png)

![](images/blueocean3.png)

![](images/jenkinsblueoceangithubsetup33.png)

- created deploy directory in the repo and added started a new jenkinsfile in it

- added some shell script to the file to trigger a build

```
pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }
    }
}
```

- configured jenkins to access the location of the jenkins file 

![](images/jenkinsfile4.png)

![](images/jenkinsbuild44.png)

![](images/jenkinsbuild444.png)

![](images/jenkinsbuild4444.png)

![](images/blueocean444.png)

![](images/blueoceanjenkinsbuild44444.png)

- created a new git branch in the repo 

- added test stage script in jenkins file under the branch to trigger a build

- merged to test for the build under main 

![](images/gitbranchfeature5.png)

```
 pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }

    stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }
    }
}
```

![](images/gitpushfeature5.png)

![](images/jenkinsbuild5.png)

![](images/jenkinsbranch5.png)

![](images/jenkinsbranch55.png)

![](images/jenkinsbranch555.png)

![](images/jenkinsbranchmaintest5.png)

- created 3 new stages - package, deploy, clean up 

- ran the build on both feature and main branch

- confirmed on my machine that the build ran the task it was supposed to

![](images/jenkinsfilecleanup6.png)

![](images/jenkinsfilecleanup66.png)

![](images/jenkinsfilecleanupoceanbuild6.png)

![](images/jenkinsfilecleanupmain6.png)

![](images/jenkinsfilecleanuplsvar6.png)


### running ansible playbook from jenkins

- installed ansible and its dependencies on jenkins server

- installed ansible plugin on jenkins ui

- added new scripts to jenkinsfile

- updated ansible.cfg with the necessary configurations

- updated necessary environments in the inventory directory

- installed postgresql and nginx role from ansible galaxy

- edited the roles to suit my play

- updated the ansible playbooks/site.yml and staticassignments file

- made necessary configurations to allow smooth running on jenkins and ansible

- introduced parametization in jenkinsfile so the different env's can be executed without needing to manually change and update them

```
pipeline {
    agent any

    parameters {
      string(name: 'inventory', defaultValue: 'dev',  description: 'This is the inventory file for the environment to deploy configuration')
    }
...
```

- ran the play on both feature and main branch

![](images/ansibleinstall7.png)

![](images/ansibleinstalldependencies7.png)

![](images/ansiblepluginjenkins7.png)

![](images/ansiblejenkinsansiblepath7.png)

![](images/ansiblejenkinsdashboardcredentials7.png)

![](images/ansiblejenkinspipelinesyntax7.png)

![](images/ansiblejenkinspipelinesyntax77.png)

![](images/ansiblecfg7.png)

![](images/ansiblegalaxypostgresql7.png)

![](images/ansiblenginxdeafultsmain7.png)

![](images/ansiblestaticassgndbyml7.png)

![](images/ansiblestaticnginx7.png)

![](images/ansibledev7.png)

![](images/ansiblejenkinsfile7.png)

![](images/ansiblejenkinsfile77.png)

![](images/ansibleplaybookssite7.png)

![](images/ansibleplay7.png)

![](images/ansibleplay77.png)

![](images/ansibleplaymain7.png)

![](images/ansibleplaymain77.png)

