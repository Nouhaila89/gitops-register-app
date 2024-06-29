pipeline {
    agent { label "Jenkins-Agent" }
    environment {
        APP_NAME = "register-app-pipeline"
        IMAGE_TAG = "latest" // Define IMAGE_TAG if not already set in the environment
    }
    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
                    git branch: 'master', credentialsId: 'github', url: 'https://${GITHUB_TOKEN}@github.com/Nouhaila89/gitops-register-app'
                }
            }
        }

        stage("Update the Deployment Tags") {
            steps {
                script {
                    sh '''
                       cat deployment.yaml
                       sed -i "s/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g" deployment.yaml
                       cat deployment.yaml
                    '''
                }
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                script {
                    sh '''
                       git config --global user.name "Nouhaila89"
                       git config --global user.email "fettahinouhaila3@gmail.com"
                       
                       git add deployment.yaml
                       git commit -m "Updated Deployment Manifest" || echo "No changes to commit"
                    '''
                    withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
                        sh '''
                           git push https://${GITHUB_TOKEN}@github.com/Nouhaila89/gitops-register-app master
                        '''
                    }
                }
            }
        }
    }
}
