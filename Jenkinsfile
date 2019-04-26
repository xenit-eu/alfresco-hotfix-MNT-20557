pipeline {
    agent any

    stages {

        stage("Clean") {
            steps {
                sh "./gradlew clean"
            }
        }

        stage("Assemble") {
            steps {
                sh "./gradlew assemble"
            }
        }
    }


    post {
        success {
            archiveArtifacts artifacts: '**/build/dist/*.amp'
        }
    }
}


