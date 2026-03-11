pipeline {
	agent any

	stages {

		stage('Clone Repo') {
			steps {
				git 'https://github.com/madushanranaweera/ci-cd-test-project.git'
			}
		}

		stage('Build Project') {
			steps {
				sh 'mvn clean package'
			}
		}

		stage('Build Docker Image') {
			steps {
				sh 'docker build -t cicd-test-project .'
			}
		}

	}
}