pipeline {
    agent any
    environment {
        VIRTUAL_ENV = 'venv'
    }
    stages {
        stage('Setup') {
            steps {
                script {
                    // Create the virtual environment
                    bat "python -m venv ${VIRTUAL_ENV}"

                    // Upgrade pip and install dependencies
                    bat """
                    call ${VIRTUAL_ENV}\\Scripts\\activate
                    python -m pip install --upgrade pip
                    pip install -r requirements.txt
                    """
                }
            }
        }
        stage('Lint') {
            steps {
                script {
                    // Activate the virtual environment and run flake8
                    bat "call ${VIRTUAL_ENV}\\Scripts\\activate && flake8 app.py"
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    // Activate the virtual environment and run pytest
                    bat "call ${VIRTUAL_ENV}\\Scripts\\activate && pytest"
                }
            }
        }
        stage('Coverage') {
            steps {
                script {
                    // Activate the virtual environment and run coverage
                    bat """
                    call ${VIRTUAL_ENV}\\Scripts\\activate
                    coverage run -m pytest
                    coverage report
                    coverage html
                    """
                }
            }
        }
        stage('Security Scan') {
            steps {
                script {
                    bat """
                    call ${VIRTUAL_ENV}\\Scripts\\activate
                    bandit -r . -o bandit_report.txt -f txt || exit 0
                    """
                }
                archiveArtifacts artifacts: 'bandit_report.txt', allowEmptyArchive: false
            }
        }
        stage('Deploy') {
            steps {
                script {
                    // Define the target deployment directory
                    def deploymentDir = 'C:\\DeployedApp'

                    // Copy application files to the deployment directory
                    bat """
                    if not exist ${deploymentDir} mkdir ${deploymentDir}
                    xcopy /E /Y *.* ${deploymentDir}\\
                    """
                    echo "Application deployed to ${deploymentDir}"
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}