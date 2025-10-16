pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('github-token')
        VENV_DIR = ".venv"
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
                
                source ${VENV_DIR}/bin/activate
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
                sh """#!/bin/bash
                set -e
                source ${VENV_DIR}/bin/activate
                python -m pytest test_app.py -v
                """
            }
        }

        stage('Build') {
            steps {
                sh 'echo "Build step placeholder (Flask apps usually don\'t need build)"'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
