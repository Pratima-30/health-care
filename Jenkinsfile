pipeline {
    agent { label 'slave_2' }

	environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockerlogin2')
	}
	
    stages {
        stage('SCM_Checkout') {
            steps {
               echo "Perform SCM Checkout"
			   git 'https://github.com/Pratima-30/BankingApp.git'
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
				sh "docker build -t pratima30/bankapp-eta-app:${BUILD_NUMBER} ."
				sh 'docker image list'
				sh "docker tag pratima30/bankapp-eta-app:${BUILD_NUMBER} pratima30/bankapp-eta-app:latest"
            }
        }
		stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}
		stage('Publish_to_Docker_Registry') {
			steps {
				sh "docker push pratima30/bankapp-eta-app"
			}
		}
        stage('Deploy to Kubernetes Cluster') {
            steps {
                script{
                  sshPublisher(publishers: [sshPublisherDesc(configName: 'k_master', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f kubernetesdeploy.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
                }
            }
        }
    }
}
