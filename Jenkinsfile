@Library('shared-lib') _ 

node {
    env.IMAGE_NAME = "mostafa200000/python-iti"
    def mvnHome = tool 'Maven-3.5.2'

    try {
        stage('Checkout') {
            git branch: 'main', url: 'https://github.com/Hassan-Eid-Hassan/python-iti.git'
        }

        stage('Build') {
            sh "${mvnHome}/bin/mvn clean package"
        }

        stage('Docker Build') {
            sh "docker build -t ${env.IMAGE_NAME}:${env.BUILD_NUMBER} ."
        }

        stage('Docker Login') {
            withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'DOCKERHUB_PASS')]) {
                // Use shared library class
                org.mylib.DockerUtils.login('mostafa200000', "${DOCKERHUB_PASS}")
            }
        }

        stage('Deploy') {
            // Use shared library function
            deployApp("${env.IMAGE_NAME}:${env.BUILD_NUMBER}", "python-app", "9000")
        }

        echo "✅ Pipeline finished successfully!"
    } catch (err) {
        echo "❌ Pipeline failed! ${err}"
        throw err
    } finally {
        echo "🧹 Cleaning workspace..."
        cleanWs()
    }
}

