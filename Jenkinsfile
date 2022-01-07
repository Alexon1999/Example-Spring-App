pipeline {
    agent none

    environment {
        DOCKER_HUB_USERNAME = credentials('DOCKER_HUB_USERNAME')
        DOCKER_HUB_PASSWORD = credentials('DOCKER_HUB_TOKEN')
        CURRENT_COMMIT = getCommitHash()
    }

    stages {
        stage('Code Anaylysis By Sonarcloud') {
            agent {
                docker {
                    image 'sonarsource/sonar-scanner-cli'
                    args '-e SONAR_HOST_URL="http://sonarcloud.io" \
                    -e SONAR_LOGIN="f7bfbbe85dee7d917eaaf755fcf8bd5105383b09" \
                    -v $PWD:/usr/src'
                }
            }
            steps {
            }
        }

        stage('Unit tests') {
            agent {
                docker {
                    image 'openjdk:17'
                    args '-v $HOME/.m2:/root/.m2'
                }
            }
            steps {
                sh 'chmod u+x ./mvnw'
                sh './mvnw test'
            }
        }

        stage('Build') {
            agent any
            when {
                beforeAgent true
                branch 'main'
            }
            steps {
                sh 'echo $DOCKER_HUB_PASSWORD | docker login -u $DOCKER_HUB_USERNAME --password-stdin'
                sh 'docker build -t $DOCKER_HUB_USERNAME/examplespringapp:$CURRENT_COMMIT .'
                sh 'docker push $DOCKER_HUB_USERNAME/examplespringapp:$CURRENT_COMMIT'
                sh 'docker logout'
            }
        }
    }
}

def getCommitHash() {
    node {
        return sh(script: 'git rev-parse --short HEAD', returnStdout: true)
    }
}