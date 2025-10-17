pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('github-token')
        VENV_DIR = ".venv"
        HOST = "0.0.0.0"
        PORT = "5000"
        APP_MODULE = "app:app"  
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/hamiiiid02/jenkins-lab.git',
                    credentialsId: 'github-token'
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
            when {
                not {
                    changeset "**/README.md"
                }
            }
            parallel {
                stage('Test App 1') {
                    steps {
                        sh """
                        . ${VENV_DIR}/bin/activate
                        python -m pytest test_app.py -v
                        """
                    }
                }
        
                stage('Test App 2') {
                    steps {
                        sh """
                        . ${VENV_DIR}/bin/activate
                        python -m pytest test_app_2.py -v
                        """
                    }
                }
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
                    --pid gunicorn.pid \
                    > gunicorn.log 2>&1 &
        
                echo "✅ Gunicorn started on http://${HOST}:${PORT}"
                """
            }
        }
    }

    post {
        success {
            emailext(
                to: "hamdonhamid67@gmail.com",
                from: "amineallali9@gmail.com",
                replyTo: "amineallali9@gmail.com",
                subject: "✅ SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """\
    <p>Good news!</p>
    <p>Build <b>${env.JOB_NAME} #${env.BUILD_NUMBER}</b> succeeded.</p>
    <p>Check details: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
    """
            )
        }
    
        failure {
            emailext(
                to: "hamdonhamid67@gmail.com",
                from: "amineallali9@gmail.com",
                replyTo: "amineallali9@gmail.com",
                subject: "❌ FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """\
    <p>Uh oh...</p>
    <p>Build <b>${env.JOB_NAME} #${env.BUILD_NUMBER}</b> failed.</p>
    <p>Check logs: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
    """
            )
        }
    }

}
