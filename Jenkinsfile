node {
    env.IMAGE_NAME = "mostafa200000/python-iti"
    def mvnHome = tool 'Maven-3.5.2'

    try {
        stage('Checkout') {
            git branch: 'main', url: 'https://github.com/Hassan-Eid-Hassan/python-iti.git'
        }

        stage('Build') {
            echo "Build Number: ${currentBuild.number}"
            if (currentBuild.number < 5) {
                error("❌ Build number is less than 5. Stopping pipeline!")
            }
            sh "${mvnHome}/bin/mvn clean package"
        }

        stage('Docker Build') {
            sh "docker build -t ${env.IMAGE_NAME}:${env.BUILD_NUMBER} ."
        }

        stage('Docker Login') {
            withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'DOCKERHUB_PASS')]) {
                sh "echo $DOCKERHUB_PASS | docker login -u mostafa200000 --password-stdin"
            }
        }

        stage('Docker Push') {
            sh "docker push ${env.IMAGE_NAME}:${env.BUILD_NUMBER}"
        }

        stage('Deploy') {
            sh """
            echo "🚀 Deploying container..."
            docker stop python-app || true
            docker rm python-app || true
            docker run -d -p 7000:8080 --name python-app ${env.IMAGE_NAME}:${env.BUILD_NUMBER}
            echo "✅ App is running on: http://<your-server-ip>:7000"
            """
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

