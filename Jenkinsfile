pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('github2-token')
        VENV_DIR = ".venv"
        HOST = "0.0.0.0"
        PORT = "5000"
        APP_MODULE = "app:app"  
        RECIPIENTS = "allali.mohamedamin@etu.uae.ac.ma, hamdonhamid67@gmail.com"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/hamiiiid02/jenkins-lab.git',
                    credentialsId: 'github2-token'
            }
        }

        stage('Setup Virtual Environment') {
            steps {
                sh """
                python3 -m venv ${VENV_DIR}
                
                . ${VENV_DIR}/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                """
            }
        }

        stage('Check Python') {
            steps {
                sh 'python3 --version'
            }
        }

        stage('Run Tests') {
            
            steps {
                sh """
                . ${VENV_DIR}/bin/activate
                mkdir -p reports
                python -m pytest test_app.py -v --html=reports/pytest-report.html --self-contained-html
                """
            }
        }
        
        stage('Publish Test Report') {
            steps {
                publishHTML(target: [
                    reportDir: 'reports',
                    reportFiles: 'pytest-report.html',
                    reportName: 'Pytest Report',
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])
            }
        }
        
        stage('Build') {
            steps {
                sh 'echo "Build step placeholder (Flask apps do not need build)"'
            }
        }

        stage('Deploy (Local with Gunicorn)') {
            steps {
                echo 'Starting Flask app locally using Gunicorn...'
                sh """#!/bin/bash
                # Activate virtual environment
                source ${VENV_DIR}/bin/activate
        
        
                setsid gunicorn --bind ${HOST}:${PORT} ${APP_MODULE} \
                    > gunicorn.log 2>&1 &
        
                echo "✅ Gunicorn started on http://${HOST}:${PORT}"
                """
            }
        }
    }

    post {
        success {
            emailext(
                to: "${env.RECIPIENTS}",
                subject: "✅ SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                <html>
                    <body>
                        <h1>Jenkins Build Notification: Flask App</h1>
                        <h2>Build Successful 🎉</h2>
                        <p>Job: <b>${env.JOB_NAME}</b></p>
                        <p>Build Number: <b>${env.BUILD_NUMBER}</b></p>
                        <p>Duration: ${currentBuild.durationString}</p>
                        <p>Build URL: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                    </body>
                </html>
                """,
                mimeType: 'text/html'
            )
        }

        failure {
            emailext(
                to: "${env.RECIPIENTS}",
                subject: "❌ FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                <html>
                    <body>
                        <h1>Jenkins Build Notification: Flask App</h1>
                        <h2>Build Failed ❌</h2>
                        <p>Job: <b>${env.JOB_NAME}</b></p>
                        <p>Build Number: <b>${env.BUILD_NUMBER}</b></p>
                        <p>Branch: <b>${env.GIT_BRANCH ?: 'N/A'}</b></p>
                        <p>Commit: <b>${env.GIT_COMMIT ?: 'N/A'}</b></p>
                        <p>Build URL: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                        <hr>
                        <p>Check console output for details.</p>
                    </body>
                </html>
                """,
                mimeType: 'text/html'
            )
        }
    }


}
