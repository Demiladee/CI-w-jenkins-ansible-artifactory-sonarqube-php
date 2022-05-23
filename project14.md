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

![](images/jenkinsfileparameters8.png)

![](images/jenkinsfileparameters88.png)

![](images/jenkinsfileparametergui8.png)

![](images/jenkinsfileparameterbuildansible8.png)

![](images/jenkinsfileparameterbuild8.png)

![](images/jenkinsfileparametermainbuild8.png)

### ci/cd pipeline for todo application

- cloned my php-todo.git repo

- installed php, its dependencies and composer tool on jenkins server

- installed ansible artifactory role

- installed plot pugin and artifactory plugin on jenkins ui

- configured artifactory on jenkins ui

- created new jenkins file on todo repo

- created multibranch pipeline on blue ocean

- created database and database user on db server

- updated database connectivity requirements

- updated jenkinsfile with pipeline config

```
pipeline {
    agent any

  stages {

     stage("Initial cleanup") {
          steps {
            dir("${WORKSPACE}") {
              deleteDir()
            }
          }
        }

    stage('Checkout SCM') {
      steps {
            git branch: 'main', url: 'https://github.com/darey-devops/php-todo.git'
      }
    }

    stage('Prepare Dependencies') {
      steps {
             sh 'mv .env.sample .env'
             sh 'composer install'
             sh 'php artisan migrate'
             sh 'php artisan db:seed'
             sh 'php artisan key:generate'
      }
    }
  }
}
```

- added unit test step to jenkinsfile

```
stage('Execute Unit Tests') {
      steps {
             sh './vendor/bin/phpunit'
      } 
```

- added code analysis step to jenkins file. output will be stored in build/logs/phploc.csv

```
stage('Code Analysis') {
  steps {
        sh 'phploc app/ --log-csv build/logs/phploc.csv'

  }
}
```

- added plot script to jenkins file

- bundled the application code into an artifact and uploaded it to artifactory

```
stage ('Package Artifact') {
    steps {
            sh 'zip -qr php-todo.zip ${WORKSPACE}/*'
     }
    }
```

- published the resulted artifact into artifactory

```
stage ('Upload Artifact to Artifactory') {
          steps {
            script { 
                 def server = Artifactory.server 'artifactory-server'                 
                 def uploadSpec = """{
                    "files": [
                      {
                       "pattern": "php-todo.zip",
                       "target": "<name-of-artifact-repository>/php-todo",
                       "props": "type=zip;status=ready"

                       }
                    ]
                 }""" 

                 server.upload spec: uploadSpec
               }
            }

        }
```

- deployed the application to the dev env by launching the ansible pipeline

```
stage ('Deploy to Dev Environment') {
    steps {
    build job: 'ansible-project/main', parameters: [[$class: 'StringParameterValue', name: 'env', value: 'dev']], propagate: false, wait: true
    }
  }
```

![](images/gitforkclone9.png)

![](images/composerinstall9.png)

![](images/phpdependencies9.png)

![](images/phpdependencies99.png)

![](images/phplocphptodoinstall9.png)

![](images/phplocphptodoinstall99.png)

![](images/artifactoryinstall9.png)

![](images/jenkinsartifactoryconfig9.png)

![](images/plotplugin9.png)

![](images/phptodojenkinsfileplotplugin9.png)

![](images/artifactorybuildparameterci9.png)

![](images/artifactoryintegrationwithjenkinsfilephptodo9.png)

![](images/artifactoryprivateipci9.png)

![](images/artifactoryroleci9.png)

![](images/artifactoryplaysiteyml9.png)

![](images/jenkinsartifactorymysqlconf9.png)

![](images/mysqlinstallonphptodo9.png)

![](images/mysqlbindaddress9.png)

![](images/mysqlbindaddress99.png)

![](images/mysqlconnectionjenkinsserver9.png)

![](images/mysqlshowdatabases9.png)

![](images/mysqlbindaddress99.png)

![](images/jenkinsfiledatabasehomestead9.png)

![](images/staticassignmentsartifactoryyml9.png)

![](images/playbookstodojob9.png)

![](images/artifactoryansibleplay9.png)

![](images/artifactory8082jfrog9.png)

![](images/artifactorybuildparameterci9.png)

![](images/packageartifactartifactoryphptodo9.png)

![](images/installingzipforpackageartifact9.png)

![](images/ansibleconfigmgtdeploytodevjob9.png)

![](images/artifactoryplaysiteyml9.png)

![](images/deployingtodevenv9.png)

![](images/deploymentymldeploytodev9.png)

![](images/envsamplephptodomysqlupdate9.png)

![](images/installingzipforpackageartifact9.png)

![](images/packageartifactartifactoryphptodo9.png)

![](images/phpartifactbuild9.png)

![](images/phpartifactbuildplot9.png)

![](images/phpartifactoryshot9.png)

![](images/phplocbuild9.png)

![](images/phplocphptodoinstall9.png)

![](images/phplocphptodoinstall99.png)

![](images/phptodojenkinsfilecodeanalysis9.png)

![](images/phptodoserver9.png)

![](images/phptodounittestbuildafterunittest9.png)

![](images/phptodojenkinsfileupdateafterbuild9.png)




