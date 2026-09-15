// Starting point. Students grow this file module by module.
pipeline {
    agent any
 
    options {
        timestamps()
    }

    parameters {
        string(name: 'VERSION', defaultValue: '1.0.0', description: 'Deployed version')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests')
    }
 
stages {
        stage('Info') {
            steps {
                echo "Rama: ${env.BRANCH_NAME}"
                echo "PR: ${env.CHANGE_ID ?: 'no es un PR'}"
                echo "Rama destino del PR: ${env.CHANGE_TARGET ?: '-'}"
                sh 'git log -1 --oneline'
            }
        }
 
        stage('Instalar dependencias') {
            steps {
                sh '''
                    python3 -m venv .venv
                    . .venv/bin/activate
                    pip install --quiet -r requirements.txt
                '''
            }
        }
 
        stage('Lint') {
            steps {
                sh '''
                    . .venv/bin/activate
                    ruff check .
                '''
            }
        }
 
        stage('Test') {
            when {
                expression { params.RUN_TESTS }
            }
            steps {
                sh '''
                    . .venv/bin/activate
                    pytest --junitxml=reports/junit.xml
                '''
            }
        }
 
        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                echo "Desplegando ${params.VERSION} desde main"
            }
        }
    }
 
    post {
        always {
            junit 'reports/junit.xml'
        }
    }
}