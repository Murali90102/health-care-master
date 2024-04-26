pipeline {
    agent { label 'test' }	

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "maven"
    }

	environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockerlogin')
	} 
    
    stages {
        stage('SCM Checkout') {
            steps {
                // Get some code from a GitHub repository
                git branch: 'main', url: 'https://github.com/Murali90102/health-care-master.git'
            }
		}
        stage('Maven Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
		}
        stage("Docker build"){
            steps {
				sh 'docker version'
				sh "docker build -t murali90102/healthcare:${BUILD_NUMBER} ."
				sh 'docker image list'
				sh "docker tag murali90102/healthcare:${BUILD_NUMBER} murali90102/healthcare:latest"
            }
        }
		stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}
		stage('Push2DockerHub') {

			steps {
				sh "docker push murali90102/healthcare:latest"
			}
		}
        stage('Deploy to Kubernetes Cluster') {
            steps {
		script {
		sshPublisher(publishers: [sshPublisherDesc(configName: 'kubernetes', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f healthcare.yml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.yml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])
		}
            }
	}
}
}

