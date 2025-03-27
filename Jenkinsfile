pipeline {
    agent
    {
        node {
            label 'mvn-slave'
        }
    }

    stages {
        stage('git checkout') {
            steps {
               git branch: 'main', url: 'https://github.com/ravdy/tweet-trend-new.git'
            }
        }
    }
}
