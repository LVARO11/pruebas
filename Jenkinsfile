pipeline {
    agent any
    tools {
        maven 'Maven'
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
                        // Leer el pom.xml con Groovy puro
                        def pomText = readFile('pom.xml')
                        def artifactId = pomText.find(/<artifactId>(.+?)<\/artifactId>/) { m, id -> id }
                        def groupId = pomText.find(/<groupId>(.+?)<\/groupId>/) { m, id -> id }
                        def version = pomText.find(/<version>(.+?)<\/version>/) { m, id -> id }
                        def packaging = pomText.find(/<packaging>(.+?)<\/packaging>/) { m, id -> id } ?: 'jar'
                        // Buscar el artefacto generado
                        def artefacto = sh(script: "ls target/*.${packaging}", returnStdout: true).trim()
                        nexusArtifactUploader(
                            nexusVersion: env.NEXUS_VERSION,
                            protocol: env.NEXUS_PROTOCOL,
                            nexusUrl: env.NEXUS_URL,
                            groupId: groupId,
                            version: version,
                            repository: env.NEXUS_REPOSITORY,
                            credentialsId: env.NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                [artifactId: artifactId, file: artefacto, type: packaging],
                                [artifactId: artifactId, file: 'pom.xml', type: 'pom']
                            ]
                        )
                    }
                }
            }
        }
    }
}
