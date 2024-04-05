properties([pipelineTriggers([githubPush()])])
pipeline {
    agent any

	tools {
        maven 'maven-test'
    }
    stages {
	    stage('Cloning') {
            steps {
                checkout([
                 $class: 'GitSCM',
                 branches: [[name: 'main']],
                 userRemoteConfigs: [[
                    url: 'https://github.com/devopscloud2021/sample-app.git',
                    //credentialsId: '',
                 ]]
                ])
			}
		}
	stage("SonarQube analysis") {
            steps {
              withSonarQubeEnv('SonarServer') {
                sh '/opt/apache-maven-3.9.6/bin/mvn sonar:sonar'
              }
            }
        }
		stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
        }
       	stage('Build'){
            steps{
			   script {
                 sh script: '/opt/apache-maven-3.9.6/bin/mvn clean package'
            }
        }
    } 	
        stage('Upload War To Nexus'){
            steps{
                nexusArtifactUploader artifacts: [
				   [
						artifactId: 'sample-app',
						classifier: '',
						file: 'target/sample-app-4.0.0.war',
						type: 'war'
				   ]
                ],				   
				credentialsId: 'nexus',
				groupId: 'in.javahome',
				nexusUrl: '13.232.27.198:8081',
				nexusVersion: 'nexus3',
				protocol: 'http',
				repository: 'sample-app',
				version: '4.0.0'
            }
        }  	
		
	}
}
