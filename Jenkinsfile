pipeline {
    agent any

    environment {
        SONARQUBE_URL = 'http://localhost:9000'
        SNYK_TOKEN = credentials('snyk-token')
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/GEETHA424/jenkins.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t my-app:latest .'
            }
        }

        stage('Run Docker Bench Security') {
            steps {
                sh 'docker run --rm --net host --pid host --userns host -v /etc:/etc:ro -v /usr/bin:/usr/bin:ro -v /usr/lib:/usr/lib:ro -v /var/run/docker.sock:/var/run/docker.sock:ro -v /var/lib:/var/lib:ro docker/docker-bench-security'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'npm install sonarqube-scanner && node_modules/.bin/sonar-scanner -Dsonar.projectKey=my-app -Dsonar.sources=. -Dsonar.host.url=${SONARQUBE_URL}'
                }
            }
        }

        stage('Snyk Security Scan') {
            steps {
                sh 'snyk test --severity-threshold=high'
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker run -d -p 8080:8080 my-app:latest'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}

