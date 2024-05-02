pipeline {
    agent any

    environment {
        ID_DOCKER = "${ID_DOCKER_PARAMS}"
        IMAGE_NAME = "website-karma-tp"
        IMAGE_TAG = "latest"
        DOCKERHUB_PASSWORD = "${DOCKERHUB_PASSWORD_PARAMS}"
        RENDER_API_TOKEN = credentials('RENDER_APIKEY')
        RENDER_SERVICE_ID = "srv-copbcoen7f5s73b3dup0"
        RENDER_DEPLOY_HOOK_URL = credentials('RENDER_DEPLOY_HOOK_URL')
    }

    triggers {
        pollSCM('H/5 * * * *') // check every 5 minutes
    }

    stages {
        stage('Build image') {
            steps {
                script {
                    sh 'docker build -t ${ID_DOCKER}/${IMAGE_NAME}:${IMAGE_TAG} .'
                }
            }
        }

        stage('Test image') {
            steps {
                script {
                    echo "Exécution des tests"
                }
            }
        }

        stage('Login and Push Image on docker hub') {
            steps {
                script {
                    sh '''
                        echo ${DOCKERHUB_PASSWORD} | docker login -u ${ID_DOCKER} --password-stdin ${DOCKERHUB_PASSWORD} // Login to Docker Hub
                        docker push ${ID_DOCKER}/${IMAGE_NAME}:${IMAGE_TAG} // Push Docker image to Docker Hub
                    '''
                }
            }
        }

        stage('Trigger Deploy to Render') {
            steps {
                withCredentials([string(credentialsId: 'RENDER_DEPLOY_HOOK_URL', variable: 'DEPLOY_HOOK_URL')]) {
                    script {
                        sh "curl -X POST ${DEPLOY_HOOK_URL}"
                    }
                }
            }
        }

    }

    post {
        success {
            mail to: 'markus.gapaz@gmail.com', // notif-jenkins@joelkoussawo.me
                 subject: "Succès du Pipeline ${env.JOB_NAME} ${env.BUILD_NUMBER}",
                 body: "Le pipeline a réussi. L'application a été déployée sur Render."
        }
        failure {
            mail to: 'markus.gapaz@gmail.com', // notif-jenkins@joelkoussawo.me
                 subject: "Échec du Pipeline ${env.JOB_NAME} ${env.BUILD_NUMBER}",
                 body: "Le pipeline a échoué. Veuillez vérifier Jenkins pour plus de détails."
        }
    }
}
