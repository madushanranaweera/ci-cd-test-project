pipeline {
	agent {
		docker {
			image 'maven:3.9.2-openjdk-21'
			args '-v /var/run/docker.sock:/var/run/docker.sock'
		}
	}

	environment {
		IMAGE_NAME = 'cicd-test-project'
		CONTAINER_NAME = 'cicd-test-project'
		HOST_PORT = '8081'
		CONTAINER_PORT = '8080'
	}

	stages {

		stage('Checkout') {
			steps {
				git branch: 'main', url: 'https://github.com/madushanranaweera/ci-cd-test-project.git'
			}
		}

		stage('Build with Maven') {
			steps {
				sh 'mvn clean package -DskipTests'
			}
		}

		stage('Build Docker Image') {
			steps {
				sh "docker build -t $IMAGE_NAME:latest ."
			}
		}

		stage('Deploy Docker Container') {
			steps {
				// Stop & remove old container if exists
				sh "docker stop $CONTAINER_NAME || true"
				sh "docker rm $CONTAINER_NAME || true"

				// Run new container
				sh "docker run -d -p $HOST_PORT:$CONTAINER_PORT --name $CONTAINER_NAME $IMAGE_NAME:latest"
			}
		}

	}

	post {
		success {
			echo 'Build, Docker image, and container deployment succeeded!'
		}
		failure {
			echo 'Something went wrong. Check the logs.'
		}
	}
}