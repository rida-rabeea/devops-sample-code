pipeline {
    agent any

    environment {
        VENV = "/opt/jenkins-venv"
        PYTHON = "/opt/jenkins-venv/bin/python3"
        PIP = "/opt/jenkins-venv/bin/pip"
    }

    stages {

        stage('Build') {
            steps {
                sh '''
                echo "Installing dependencies inside virtual environment..."
                $PIP install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                echo "Running tests using venv Python"
                $PYTHON -m unittest discover -s .
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                mkdir -p ${WORKSPACE}/python-app-deploy
                cp app.py ${WORKSPACE}/python-app-deploy/
                '''
            }
        }

        stage('Run Application') {
            steps {
                sh '''
                echo "Starting Flask app using venv Python..."
                nohup $PYTHON ${WORKSPACE}/python-app-deploy/app.py > ${WORKSPACE}/python-app-deploy/app.log 2>&1 &
                echo $! > ${WORKSPACE}/python-app-deploy/app.pid
                '''
            }
        }

        stage('Test Application') {
            steps {
                sh '''
                echo "Testing deployed app..."
                $PYTHON test_app.py
                '''
            }
        }

    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for more details.'
        }
    }
}
