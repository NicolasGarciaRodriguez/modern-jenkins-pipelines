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
                sh(script: 'docker images -a')
                sh(script: """
                    cd azure-vote/
                    docker images -a
                    docker build -t jenkins-pipeline .
                    docker images -a
                    cd ..
                """)
            }
        }
        stage('Start test app') {
            steps{
                sh(script: """
                    docker compose up -d
                    chmod +x ./scripts/test_container.sh
                    ./scripts/test_container.sh
                """)
            }
            post {
                success {
                    echo "App started successfully!"
                }
                failure {
                    echo "App failed to start"
                }
            }
        }
        stage('Run tests') {
            steps{
                sh(script: """
                    pytest ./tests/test_sample.py
                """)
            }
        }
        stage('Stop test app') {
            steps{
                sh(script: """
                    docker compose down
                """)
            }
        }
        stage('Push container') {
            steps{
                echo "Workspace is $WORKSPACE"
                dir("$WORKSPACE/azure-vote") {
                    script{
                        docker.withRegistry('https://index.docker.io/v1/', 'DockerHub') {
                            def image = docker.build('nicogarcia97/jenkins-course:latest')
                            image.push()
                        }
                    }
                }
            }
        }
        stage('scan'){
            steps{
                // Install trivy
                sh(script: """
                    curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /usr/local/bin v0.18.3
                    curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/html.tpl > html.tpl
                """)
                //Scan
                sh(script: """
                    trivy nicogarcia97/jenkins-course
                """)
            }
        }
    }
}
