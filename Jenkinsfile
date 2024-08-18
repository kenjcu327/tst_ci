pipeline {
    agent any

    environment {
        BUILD_DIR = "build"
        REPO_URL = "https://github.com/kenjcu327/tstst.git" // Replace with your repository URL
    }

    stages {
        stage('Checkout') {
            steps {
                // Clone the repository
                git url: "${REPO_URL}", branch: 'master'
            }
        }
        
        stage('Prepare') {
            steps {
                // Clean workspace and create build directory
                deleteDir()
                sh "mkdir -p ${BUILD_DIR}"
            }
        }

        stage('Build with AddressSanitizer') {
            steps {
                dir(BUILD_DIR) {
                    sh 'cmake .. -DENABLE_ASAN=ON'
                    sh 'make'
                }
                // Run the executable to check for issues
                sh "./MemoryThreadUBProject"
            }
        }
        
        stage('Build with UndefinedBehaviorSanitizer') {
            steps {
                dir(BUILD_DIR) {
                    sh 'cmake .. -DENABLE_UBSAN=ON'
                    sh 'make'
                }
                // Run the executable to check for issues
                sh "./MemoryThreadUBProject"
            }
        }
        
        stage('Build with ThreadSanitizer') {
            steps {
                dir(BUILD_DIR) {
                    sh 'cmake .. -DENABLE_TSAN=ON'
                    sh 'make'
                }
                // Run the executable to check for issues
                sh "./MemoryThreadUBProject"
            }
        }

        stage('Build with MemorySanitizer') {
            steps {
                dir(BUILD_DIR) {
                    sh 'cmake .. -DENABLE_MSAN=ON'
                    sh 'make'
                }
                // Run the executable to check for issues
                sh "./MemoryThreadUBProject"
            }
        }

        stage('Build with LeakSanitizer') {
            steps {
                dir(BUILD_DIR) {
                    sh 'cmake .. -DENABLE_LSAN=ON'
                    sh 'make'
                }
                // Run the executable to check for issues
                sh "./MemoryThreadUBProject"
            }
        }
    }

    post {
        always {
            // Clean up workspace after build
            cleanWs()
        }

        success {
            // Archive artifacts if needed
            archiveArtifacts artifacts: "${BUILD_DIR}/**/*", allowEmptyArchive: true
        }

        failure {
            // Notify about failure (e.g., via email)
            emailext(
                subject: "Jenkins Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Build failed in Jenkins. Check console output at ${env.BUILD_URL} for details.",
                recipientProviders: [[$class: 'DevelopersRecipientProvider']]
            )
        }
    }
}
