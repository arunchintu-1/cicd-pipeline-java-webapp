pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install package'
            }
        }

        stage('Set Environment Variables') {
            steps {
                script {
                    def pom = readMavenPom file: 'pom.xml'
                    env.ArtifactId = pom.getArtifactId()
                    env.Version = pom.getVersion()
                    env.GroupId = pom.getGroupId()
                    env.Name = pom.getName()
                }
            }
        }

        stage('Test') {
            steps {
                echo "Testing..."
                // Add actual test commands if needed
            }
        }

        stage('Print Environment variables') {
            steps {
                echo "Artifact ID: ${env.ArtifactId}"
                echo "Group ID: ${env.GroupId}"
                echo "Version: ${env.Version}"
                echo "Name: ${env.Name}"
            }
        }

        stage('Deploy to Docker') {
            steps {
                echo 'Deploying...'
                // Add your Docker deployment commands here
            }
        }
    }
}
