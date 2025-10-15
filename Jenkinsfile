pipeline {
    agent any

    tools {
        maven 'maven' // Maven tool configured in Jenkins
    }

    environment {
        TOMCAT_HOME = "/tmp/tomcat" // Update if your Tomcat path is different
    }

    stages {
        stage('Build') {
            steps {
                echo "Building project..."
                sh 'mvn clean package'
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
                echo "Running tests..."
                sh 'mvn test'
            }
        }

        stage('Print Environment Variables') {
            steps {
                echo "Artifact ID: ${env.ArtifactId}"
                echo "Group ID: ${env.GroupId}"
                echo "Version: ${env.Version}"
                echo "Name: ${env.Name}"
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                echo "Deploying WAR to Tomcat..."
                sh """
                    cp target/${ArtifactId}-${Version}.war ${TOMCAT_HOME}/webapps/
                    echo "WAR copied to ${TOMCAT_HOME}/webapps/"
                """
            }
        }

        stage('Expose Target Folder') {
            steps {
                echo "Copying target folder to Tomcat for external access..."
                sh """
                    rm -rf ${TOMCAT_HOME}/webapps/target
                    cp -r target ${TOMCAT_HOME}/webapps/
                    echo "Target folder exposed at http://<server-ip>:8080/target/"
                """
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully! Access your app at:"
            echo "WAR: http://<server-ip>:8080/${ArtifactId}-${Version}/"
            echo "Target folder: http://<server-ip>:8080/target/"
        }
        failure {
            echo "Pipeline failed. Check the Jenkins console for errors."
        }
    }
}
