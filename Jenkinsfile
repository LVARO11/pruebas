pipeline {
    agent any
    tools {
        maven 'Maven' // Debe coincidir con el nombre en Global Tool Configuration de Jenkins
    }
    environment {
        NEXUS_VERSION = 'nexus3'
        NEXUS_PROTOCOL = 'http'
        NEXUS_URL = 'localhost:8081'
        NEXUS_REPOSITORY = 'artefactos-local'
        NEXUS_CREDENTIAL_ID = 'nexus-cred'
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/LVARO11/pruebas.git', branch: 'main'
            }
        }
        stage('Build') {
            steps {
                dir('Back-End') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }
        stage('Subir a Nexus') {
            steps {
                dir('Back-End') {
                    script {
                        // Necesitas el plugin Pipeline Utility Steps para esto
                        def pom = readMavenPom file: 'pom.xml'
                        def artefacto = findFiles(glob: "target/*.${pom.packaging}")[0]
                        nexusArtifactUploader(
                            nexusVersion: env.NEXUS_VERSION,
                            protocol: env.NEXUS_PROTOCOL,
                            nexusUrl: env.NEXUS_URL,
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: env.NEXUS_REPOSITORY,
                            credentialsId: env.NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                [artifactId: pom.artifactId, file: artefacto.path, type: pom.packaging],
                                [artifactId: pom.artifactId, file: 'pom.xml', type: 'pom']
                            ]
                        )
                    }
                }
            }
        }
    }
}
