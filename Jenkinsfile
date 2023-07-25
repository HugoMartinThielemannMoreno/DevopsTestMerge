pipeline {
    agent any

    tools {
        maven "MAVEN_HOME"
        jdk "JAVA_HOME"
    }

    environment {
        DISABLE_AUTH = 'true'
        DB_ENGINE    = 'sqlite'
    }

    stages {
        stage('Hello') {
            steps {
                echo "Database engine is ${DB_ENGINE}"
                echo "DISABLE_AUTH is ${DISABLE_AUTH}"
                echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
                echo "Job Name: ${env.JOB_NAME}"
                echo "Java Home: ${env.JAVA_HOME}"
            }
        }

        stage('Git Polling') {
            steps {
                git branch: 'master', credentialsId: 'coca', url: 'git@github.com:HugoMartinThielemannMoreno/rick-and-morty-api-client.git'
            }
        }

        stage('BUILD CON MAVEN') {
            steps {
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            post {
                success {
                    echo 'Archiving Artifacts'
                    archiveArtifacts "target/*.jar"
                }
            }
        }

        stage('Test Maven') {
            steps {
                sh "mvn test"
            }
        }

        stage('Test soapui') {
            steps {
                sh "${env.SOAPUI_HOME}/bin/testrunner.sh -s get-characters-test-suite -c get-characters-test-case -I /opt/REST-Project-1-soapui-project.xml"
            }
        }

        stage('Execute Jmeter') {
            steps {
                sh 'pwd'
                sh 'mvn clean verify -Djmeter.version=5.6 -Djmeter.check.results.threshold=0'
            }
        }
    }

post {
    always {
        script {
            def errorThreshold = 0 // Establecer el umbral de aceptación en 0

            // Leer los resultados de JMeter
            def results = readFile('result.jtl')
            def errorCount = results.tokenize('\n').findAll { it.contains('false') }.size()

            // Verificar si se supera el umbral de errores
            if (errorCount > errorThreshold) {
                error "La cantidad de errores (${errorCount}) supera el umbral de aceptación (${errorThreshold})"
            }
        }
    }
}
}
