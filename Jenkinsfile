pipeline {
    agent { label 'Banking_slave' }

	environment {	
		DOCKERHUB_CREDENTIALS=credentials('Banking_app')
	}
	
    stages {
        stage('SCM_Checkout') {
            steps {
               echo "Perform SCM Checkout"
			   git 'https://github.com/GaneshNimmakayala/BankingApp.git'			   
            }
        }
        stage('Application Build') {
            steps {
                echo "Perform Application Build"
				sh 'mvn clean package'
            }
        }
        stage('Build Docker Image') {
            steps {
				sh 'docker version'
				sh "docker build -t ganeshnimmakayala/banking-app:${BUILD_NUMBER} ."
				sh 'docker image list'
				sh "docker tag ganeshnimmakayala/banking-app:${BUILD_NUMBER} ganeshnimmakayala/banking-app:latest"
            }
        }
		stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}
		stage('Publish_to_Docker_Registry') {
			steps {
				sh "docker ganeshnimmakayala/banking-app:latest"
			}
		}
        stage('Deploy to Kubernetes Cluster') {
            steps {
                script{
                   sshPublisher(publishers: [sshPublisherDesc(configName: 'Kubernetes_Master', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f kubernetesdeploy.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '/root/kubernetes', remoteDirectorySDF: false, removePrefix: 'target/', sourceFiles: 'target/*.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
                 }
            }
        }
    }
}
