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
            }
        }

        stage('Publish to Nexus') {
            steps {
                script {
                    def NexusRepo = env.Version.endsWith("SNAPSHOT") ? "MyLab-SNAPSHOT" : "MyLab-RELEASE"

                    nexusArtifactUploader artifacts: [
                        [
                            artifactId: env.ArtifactId,
                            classifier: '',
                            file: "target/${env.ArtifactId}-${env.Version}.war",
                            type: 'war'
                        ]
                    ],
                    credentialsId: 'nexus',
                    groupId: env.GroupId,
                    nexusUrl: '10.0.0.167:8081',
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    repository: NexusRepo,
                    version: env.Version
                }
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
            }
        }
    }
}
