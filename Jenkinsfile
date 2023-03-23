pipeline {
    agent any

    stages {
        stage('Verify branch') {
            steps {
                echo "$GIT_BRANCH"
            }
        }
        stage('Docker build') {
            steps{
                bash """
                    docker images -a
                    cd azure-vote/
                    docker images -a
                    docker build -t jenkins-pipeline
                    docker images -a
                    cd ..
                """
            }
        }
    }
}
