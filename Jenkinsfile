pipeline {
    agent any
    environment {
        TOMCAT_SERVER = "http://localhost:8080/"
    }
    parameters {
        choice(name: 'BUILD_TOOL', choices: ['maven', 'gradle', 'ant'], description: 'Choose the build tool')
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
                    if (params.BUILD_TOOL == "maven") {
                        withMaven(maven: 'M3') {
                            sh 'mvn clean package'
                        }
                    } else if (params.BUILD_TOOL == "gradle") {
                        sh './gradlew clean build'
                    } else if (params.BUILD_TOOL == "ant") {
                        sh 'ant build'
                    }
                }
            }
        }
        stage('Unit Tests') {
            steps {
                sh params.BUILD_TOOL == "maven" ? 'mvn test' : (params.BUILD_TOOL == "gradle" ? './gradlew test' : 'ant test')
            }
        }
        stage('Deploy to Tomcat') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'tomcat-credentials', usernameVariable: 'TOMCAT_USER', passwordVariable: 'TOMCAT_PASSWORD')]) {
                    script {
                        def warFile = sh(script: "ls target/*.war | head -n 1", returnStdout: true).trim()
                        sh """
                        curl -u ${TOMCAT_USER}:${TOMCAT_PASSWORD} --upload-file ${warFile} \\
                            ${TOMCAT_SERVER}/manager/text/deploy?path=/sample-app&update=true
                        """
                    }
                }
            }
        }
    }
    post {
        always {
            junit '**/target/surefire-reports/*.xml' // Generates test reports for Maven
        }
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for details.'
        }
    }
}
