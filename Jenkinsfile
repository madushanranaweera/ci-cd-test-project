pipeline {
	agent none  // ← No global agent

	environment {
		IMAGE_NAME = 'cicd-test-project'
		CONTAINER_NAME = 'cicd-test-project'
		HOST_PORT = '8081'
		CONTAINER_PORT = '8080'
	}

	stages {

		stage('Checkout') {
			agent any  // ← Runs on Jenkins host
			steps {
				git branch: 'main', url: 'https://github.com/madushanranaweera/ci-cd-test-project.git'
				stash includes: '**', name: 'source-code'  // ← Save files for next stage
			}
		}

		stage('Build with Maven') {
			agent {
				docker {
					image 'maven:3.9.2-openjdk-21'
					args '-v $HOME/.m2:/root/.m2'  // Cache Maven deps
				}
			}
			steps {
				unstash 'source-code'  // ← Restore files
				sh 'mvn clean package -DskipTests'
				stash includes: 'target/*.jar', name: 'jar-file'  // ← Save JAR
			}
		}

		stage('Build Docker Image') {
			agent any  // ← Runs on Jenkins host (has Docker)
			steps {
				unstash 'source-code'
				unstash 'jar-file'  // ← Restore JAR into workspace
				sh "docker build -t ${IMAGE_NAME}:latest ."
			}
		}

		stage('Deploy Docker Container') {
			agent any  // ← Runs on Jenkins host (has Docker)
			steps {
				sh "docker stop ${CONTAINER_NAME} || true"
				sh "docker rm ${CONTAINER_NAME} || true"
				sh "docker run -d -p ${HOST_PORT}:${CONTAINER_PORT} --name ${CONTAINER_NAME} ${IMAGE_NAME}:latest"
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