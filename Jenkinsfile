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
                    env.ArtifactId = readMavenPom().getArtifactId()
                    env.Version = readMavenPom().getVersion()
                    env.GroupId = readMavenPom().getGroupId()
                    env.Name = readMavenPom().getName()
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Testing...'
            }
        }

        stage('Publish to Nexus') {
            steps { 
                script {
                    def NexusRepo = env.Version.endsWith("SNAPSHOT") ? "MyLab-SNAPSHOT" : "MyLab-RELEASE"
                    
                    nexusArtifactUploader artifacts: 
                    [
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
                echo "Artifact ID is '${env.ArtifactId}'"
                echo "Group ID is '${env.GroupId}'"
                echo "Version is '${env.Version}'"
                echo "Name is '${env.Name}'"
            }
        }

        stage('Deploy to Docker') {
            steps {
                echo 'Deploying...'
                sshPublisher(publishers: 
                [sshPublisherDesc(
                    configName: 'ansible-controller', 
                    transfers: [
                        sshTransfer(
                            sourceFiles: 'download-deploy.yaml, hosts',
                            remoteDirectory: '/playbooks',
                            cleanRemote: false,
                            execCommand: 'cd playbooks/ && ansible-playbook download-deploy.yaml -i hosts', 
                            execTimeout: 120000
                        )
                    ], 
                    usePromotionTimestamp: false, 
                    useWorkspaceInPromotion: false, 
                    verbose: false)
                ])
            }
        }
    }
}
