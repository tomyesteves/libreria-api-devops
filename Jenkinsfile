// Starting point. Students grow this file module by module.
pipeline {
    agent any
 
    options {
        timestamps()
    }

    parameters {
        choice(name: 'ENVIRONMENT', choices: ['dev', 'qa', 'prod'], description: 'Deployed environment')
        string(name: 'VERSION', defaultValue: '1.0.0', description: 'Deployed version')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests')
    }
 
    stages {
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

        stage('Aprobacion') {
            when {
                expression { params.ENVIRONMENT == 'prod' }
            }
            steps {
                input message: "Desplegar la version ${params.VERSION} a PRODUCCION?", ok: 'Si, desplegar'
            }
        }

        stage('Deploy') {
            steps {
                echo "Desplegando ${params.VERSION} al ambiente ${params.ENVIRONMENT}"
                sh 'echo "Entorno desde el shell: $ENVIRONMENT, version $VERSION"'
            }
        }
    }
 
    post {
        always {
            junit 'reports/junit.xml'
        }
    }
}