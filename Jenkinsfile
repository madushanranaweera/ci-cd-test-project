pipeline {
	agent any  // ← Run everything on Jenkins host directly

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
				// Run Maven inside a temporary Docker container
				sh "docker run --rm -v \${WORKSPACE}:/app -w /app maven:3.9.2-openjdk-21 mvn clean package -DskipTests"
			}
		}

		stage('Build Docker Image') {
			steps {
				sh "docker build -t ${IMAGE_NAME}:latest ."
			}
		}

		stage('Deploy Docker Container') {
			steps {
				sh "docker stop ${CONTAINER_NAME} || true"
				sh "docker rm ${CONTAINER_NAME} || true"
				sh "docker run -d -p ${HOST_PORT}:${CONTAINER_PORT} --name ${CONTAINER_NAME} ${IMAGE_NAME}:latest"
			}
		}
	}

	post {
		success {
			echo 'Deployment succeeded!'
		}
		failure {
			echo 'Something went wrong. Check the logs.'
		}
	}
}