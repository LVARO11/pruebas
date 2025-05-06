pipeline {
    agent any

    environment {
        NEXUS_URL = 'http://localhost:8081'  // Asegúrate de usar la IP correcta si no es localhost
        NEXUS_REPO = 'artefactos-local'
    }

    tools {
        maven 'Maven'
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'git-pat', url: 'https://github.com/LVARO11/pruebas', branch: 'main'
            }
        }

        stage('Publish JAR to Nexus') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'nexus-cred',
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    sh """
                        mvn -f Back-End/pom.xml deploy \
                          -DskipTests \
                          -DaltDeploymentRepository=nexus::default::${NEXUS_URL}/repository/${NEXUS_REPO}/ \
                          -Dnexus.username=\$NEXUS_USER \
                          -Dnexus.password=\$NEXUS_PASS
                    """
                }
            }
        }
    }
}


