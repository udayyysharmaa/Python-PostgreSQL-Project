pipeline {
    agent any
    tools{
        jdk 'jdk17'
    }
    environment{
        SONAR_HOME = tool "sonar-scanner"
    }

    stages {
        stage('Clone the Code ') {
            steps {
                git branch: 'main', url: 'https://github.com/udayyysharmaa/Python-PostgreSQL-Project.git'
            }
        }
        stage('Sonarqube checker') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=PythonProject -Dsonar.projectKey=Python12234"
                }
            }
        }
        stage('SonarQuebe Quality Check') {
            steps {
                timeout(time: 1, unit: "MINUTES"){
                    waitForQualityGate abortPipeline: false
                }

            }
        }
        stage('Project Build') {
            steps {
                sh 'docker build -t projectpython:latest .'
            }
        }
        stage('Trivy check') {
            steps {
                sh 'trivy image  projectpython:latest '
            }
        }
        stage('Push to Docker hub ') {
            steps {
                withCredentials([usernamePassword(credentialsId:"dockerhubcred",passwordVariable:"dockerPass",usernameVariable:"dockerUser")]){
                    sh "docker login -u ${env.dockerUser} -p ${env.dockerPass}"
                    sh "docker tag projectpython:latest ${env.dockerUser}/projectpython:latest"
                    sh "docker push ${env.dockerUser}/projectpython:latest"
                    
                }

            }
        }
        stage('Code Deploy') {
            steps {
                sh 'docker-compose up -d'
            }
        }
    }
}
