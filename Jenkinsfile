pipeline {
    agent any

    environment {
        GIT_CRED_ID = 'github-pat'  // Jenkins credentials ID for GitHub PAT
    }

    stages {
        stage('Clone Repository') {
            steps {
                withCredentials([usernamePassword(credentialsId: GIT_CRED_ID, usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
                    git branch: 'main', url: "https://${GIT_USER}:${GIT_TOKEN}@github.com/PadmavathyNarayanan/jenkins-pipeline-demo.git"
                }
            }
        }

        stage('Build & Test All Branches') {
            steps {
                script {
                    def branches = ['develop', 'main']

                    for (branch in branches) {
                        echo "Processing Branch: ${branch}"

                        // Checkout each branch
                        checkout([$class: 'GitSCM', 
                            branches: [[name: "*/${branch}"]], 
                            userRemoteConfigs: [[url: "https://${GIT_USER}:${GIT_TOKEN}@github.com/PadmavathyNarayanan/jenkins-pipeline-demo.git"]]
                        ])

                        try {
                            sh 'mvn clean test'
                            echo "Build & Test Successful for ${branch}"
                        } catch (Exception e) {
                            echo "Build & Test Failed for ${branch}"
                        }
                    }
                }
            }
        }

        stage('Deploy if Main Branch') {
            when {
                expression { env.GIT_BRANCH == 'main' } // Corrected condition
            }
            steps {
                echo "Deploying Application from Main Branch"
                sh 'scp target/app.jar user@server:/deployments/' // Ensure SSH authentication is configured
            }
        }
    }
}
