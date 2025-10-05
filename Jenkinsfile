pipeline {
    agent any
    environment {
         PATH = "/usr/local/bin:$PATH"
        IMAGE = "flask-book-manager:${env.BUILD_NUMBER}"
    }
    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Build') {
            steps {
                sh 'docker build -t $IMAGE .'
            }
        }
        stage('Test') {
            steps {
                sh 'docker run --rm $IMAGE pytest -q'
            }
        }
        stage('Code Quality') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'sonar-scanner -Dsonar.projectKey=FlaskBookManager'
                }
            }
        }
        stage('Security Scan') {
            steps {
                sh 'trivy image $IMAGE'
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker run -d -p 5000:5000 $IMAGE'
            }
        }
        stage('Monitoring Check') {
            steps {
                sh 'curl -f http://localhost:5000/health || exit 1'
            }
        }
        stage('Release') {
            steps {
                sh 'git tag -a v1.0 -m "Stable release"'
                sh 'git push origin v1.0'
            }
        }
    }
}

