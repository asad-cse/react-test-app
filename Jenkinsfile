pipeline {
    agent any

    tools {
        nodejs 'NodeJS'   // MUST match the name under Manage Jenkins → Global Tool Configuration
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/asad-cse/react-test-app.git', branch: 'main'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }
        stage('Deployment to Lightsail Docker'){
            steps{
                sh """
                scp -P 59000 -o StrictHostKeyChecking=no -r build/bundle.js ubuntu@52.74.177.117:/var/www/app/react-app/

                ssh -p 59000 -o StrictHostKeyChecking=no ubuntu@52.74.177.117 '
                docker rm -f react-app 2>/dev/null || true
                docker run -d --name react-app -p 8080:80 \
                    -v /var/www/app/react-app:/usr/share/nginx/html \
                    -v /home/ubuntu/nginx-conf:/etc/nginx/conf.d \
                    nginx
                '
                """
            }
        }

        stage('Archive Build') {
            steps {
                archiveArtifacts artifacts: 'build/**', fingerprint: true
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        failure {
            echo 'The build has failed'
        }
        success {
            echo 'The build was successful'
        }
    }
}