pipeline {
    agent any

    environment {
        // Optional: if you configured a named Python installation in Jenkins, reference it here
        PYTHON_EXE = 'python' // points to your Windows Python with pip
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/alhusseain/JenkinsLab.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat """
                %PYTHON_EXE% -m venv venv
                venv\\Scripts\\python.exe -m pip install --upgrade pip
                venv\\Scripts\\python.exe -m pip install -r requirements.txt
                """
            }
        }

        stage('Lint & Static Analysis') {
            steps {
                bat """
                venv\\Scripts\\python.exe -m pylint app --exit-zero
                venv\\Scripts\\bandit -r app
                """
            }
        }

        stage('Unit Tests') {
            steps {
                bat """
                venv\\Scripts\\python.exe -m pytest --cov=app --cov-report=xml
                """
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
                // Use PowerShell Compress-Archive for Windows
                bat 'powershell Compress-Archive -Path app\\* -DestinationPath artifact.zip'
            }
        }
    }
}
