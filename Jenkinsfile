node {
    // Define the branches to be built
    def branches = ['develop', 'main']

    stage('Clone Repository') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-pat', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
                    git url: "https://$GIT_USER:$GIT_TOKEN@github.com/PadmavathyNarayanan/jenkins-pipeline-demo.git", branch: 'main'
                }
            }
        }
    stage('Build & Test All Branches') {
        for (branch in branches) {
            stage("Build & Test ${branch}") {
                // Checkout the specific branch
                checkout([$class: 'GitSCM', 
                    branches: [[name: "*/${branch}"]], 
                    userRemoteConfigs: [[url: 'https://github.com/PadmavathyNarayanan/jenkins-pipeline-demo.git ']]
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

    stage('Deploy if Main Branch') {
        if (env.BRANCH_NAME == 'main') {
            echo "Deploying Application from Main Branch"
            sh 'scp target/app.jar user@server:/deployments/'
        } else {
            echo " Skipping Deployment for Non-Main Branch: ${env.BRANCH_NAME}"
        }
    }
}

