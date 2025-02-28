pipeline {
    agent any
    environment {
        TOMCAT_SERVER = "http://localhost:8080/"
        TOMCAT_USER = 'admin' // Replace with your Tomcat username
        TOMCAT_PASSWORD = 'password' // Replace with your Tomcat password
    }
    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/YashPathare/DM4.git', branch: 'main'
            }
        }
        stage('Build') {
            steps {
                script {
                    def buildTool = "maven" // Change to "gradle" or "ant" as needed
                    if (buildTool == "maven") {
                        withMaven(maven: 'M3') {
                            sh 'mvn clean package'
                        }
                    } else if (buildTool == "gradle") {
                        sh './gradlew clean build'
                    } else if (buildTool == "ant") {
                        sh 'ant build'
                    }
                }
            }
        }
        stage('Unit Tests') {
            steps {
                sh 'mvn test' // Update for Gradle (`./gradlew test`) or Ant (`ant test`)
            }
        }
        stage('Deploy to Tomcat') {
            steps {
                script {
                    def warFile = 'target/sample-app.war'
                    sh """
                    curl -u ${TOMCAT_USER}:${TOMCAT_PASSWORD} --upload-file ${warFile} \\
                        ${TOMCAT_SERVER}/manager/text/deploy?path=/sample-app&update=true
                    """
                }
            }
        }
    }
    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for details.'
        }
    }
}
