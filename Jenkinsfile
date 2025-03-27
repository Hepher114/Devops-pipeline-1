pipeline {
    agent {
        node {
            label 'maven-node'
        }
    }
    
    environment {
        PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', 
                url: 'https://github.com/ravdy/tweet-trend-new.git'
            }
        }
        stage('build') {
            steps {
                sh 'mvn clean deploy'
            }
        }
    }
}
