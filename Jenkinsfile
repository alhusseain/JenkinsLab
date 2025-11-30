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
                python -m pip install --upgrade pip
                venv\\Scripts\\python -m pip install -r requirements.txt
                '''
            }
        }

        stage('Lint & Static Analysis') {
            steps {
                bat '''
                venv\\Scripts\\python -m pylint app --exit-zero
                venv\\Scripts\\bandit -r app
                '''
            }
        }

        stage('Unit Tests') {
            steps {
                bat '''
                venv\\Scripts\\python -m pytest --cov=app --cov-report=xml
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
                // Using PowerShell Compress-Archive for Windows
                bat 'powershell Compress-Archive -Path app\\* -DestinationPath artifact.zip'
            }
        }
    }
}
