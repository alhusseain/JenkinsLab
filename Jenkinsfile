pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/alhusseain/JenkinsLab.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat '''
                python -m venv venv
                venv\\Scripts\\activate
                pip install -r requirements.txt
                '''
            }
        }

        stage('Lint & Static Analysis') {
            steps {
                bat '''
                venv\\Scripts\\activate
                pylint app --exit-zero
                bandit -r app
                '''
            }
        }

        stage('Unit Tests') {
            steps {
                bat '''
                venv\\Scripts\\activate
                pytest --cov=app --cov-report=xml
                '''
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('MySonarQubeServer') {
                    bat 'sonar-scanner'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Package Application') {
            steps {
                bat 'powershell Compress-Archive -Path app\\* -DestinationPath artifact.zip'
            }
        }
    }
}
