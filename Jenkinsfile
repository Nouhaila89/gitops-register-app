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
                git branch: 'master', credentialsId: 'github', url: 'https://github.com/Nouhaila89/gitops-register-app'
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
                       git config --global user.email "fettahi.nouhaila@ensi.ma"
                       git add deployment.yaml
                       git commit -m "Updated Deployment Manifest"
                    '''
                    withCredentials([usernamePassword(credentialsId: 'github', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                        sh '''
                           git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/Nouhaila89/gitops-register-app master
                        '''
                    }
                }
            }
        }
    }
}
