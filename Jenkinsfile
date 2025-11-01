@Library('shared-lib') _  // Load shared library

node {
    def IMAGE_NAME = "mostafa200000/python-iti"
    def BUILD_NUM = env.BUILD_NUMBER
    def mvnHome = tool 'Maven-3.5.2'

    try {
        stage('Checkout') {
            git branch: 'main', url: 'https://github.com/Hassan-Eid-Hassan/python-iti.git'
        }

        stage('Build') {
            echo "Build Number: ${BUILD_NUM}"
            if (BUILD_NUM.toInteger() < 5) {
                error("❌ Build number is less than 5. Stopping pipeline!")
            }

            sh "${mvnHome}/bin/mvn clean package"
        }

        stage('Docker Build') {
            sh "docker build -t ${IMAGE_NAME}:${BUILD_NUM} ."
        }

        stage('Docker Login') {
            dockerLogin('mostafa200000', 'dockerhub-pass')  // shared library step
        }

        stage('Docker Push') {
            sh "docker push ${IMAGE_NAME}:${BUILD_NUM}"
        }

        stage('Deploy') {
            deployApp("${IMAGE_NAME}:${BUILD_NUM}", "python-app", "9000") // shared library step
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
