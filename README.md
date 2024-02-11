## How to run using AWS CodeBuild
1. Add code to your GitHub account and add repo
2. Create account in SonarCloud
3. Create AWS CodeBuild -> Use Ubuntu image as build source -> link source code repo as GitHub
4. Login to Sonarcloud -> Accounts -> Generate unique token and save it
5. Update buildspec.yml with sonar.key and sonar.org and sonar_token
6. Build CodeBuild and analyse scans in SonarCloud

## How to run using Jenkins Pipeline
1. Add code to your GitHub account and add repo
2. Create account in SonarCloud
3. Login to Sonarcloud -> Accounts -> Generate unique token and save it
4. Add Sonar Plugin to Jenkins and Create Jenkins Pipeline job
6. Add Pipeline script code 
pipeline {
    agent any
	    tools {
        maven 'local_maven'
      }
    stages {
        stage('Checkout') {
            steps {
            checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/anandkr01045/aws-sast-sonar.git']]])            
            }
        }        
        stage ("Sonarqube Analysis") {
            steps {
                bat 'mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey="<project_key>" -Dsonar.organization="<sonar_organization>" -Dsonar.host.url="https://sonarcloud.io" -Dsonar.login=<sonar_token>'
            }
        }
	  }
}
7. Build Now and view results to the sonar dashboard
# Java Reachability Playground

This is an intentionally vulnerable application. It was purposely designed to demonstrate the capabilities of Snyk's Reachable
Vulnerabilities feature and includes both a "Reachable" vulnerability (with a direct data flow to the vulnerable function) and a "Potentially Reachable" vulnerability (where only partial data exists for determining reachability).


## Included vulnerabilities
### [Arbitrary File Write via Archive Extraction](https://app.snyk.io/vuln/SNYK-JAVA-ORGND4J-72550)
An exploit is using a vulnerability called [ZipSlip](https://snyk.io/research/zip-slip-vulnerability) - a critical vulnerability discovered 
by Snyk, which typically results in remote command execution. As part of the exploit, a special zip archive is 
crafted (attached as `malicious_file.zip`). When this file is extracted by a vulnerable function, it will create a file 
called `good.txt` in the folder `unzipped`, but it will also create a file called `evil.txt` in the `/tmp/` folder. 
This example is not dangerous, of course, but demonstrates the risk the vulnerability poses - imagine overwriting `.ssh/authorized_keys` or another sensitive file.

