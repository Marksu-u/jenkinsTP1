pipeline {
    agent any

    environment {
        
        DOCKER_REGISTRY = "ton_registry"
        DOCKER_IMAGE_NAME = "ton_image"
        DOCKER_REGISTRY_CREDENTIALS_ID = "ton_id_de_credentials"
    }

    stages {
        stage('Build') {
            steps {
                
                echo 'Lancement du build...'
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                
                echo 'Lancement des tests...'
                sh 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                
                echo 'Construction de l\'image Docker...'
                sh 'docker build -t $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME .'
            }
        }

        stage('Push Image to Registry') {
            steps {
                
                echo 'Envoi de l\'image Docker vers le registre...'
                script {
                    docker.withRegistry('https://$DOCKER_REGISTRY', DOCKER_REGISTRY_CREDENTIALS_ID) {
                        def appImage = docker.build("$DOCKER_REGISTRY/$DOCKER_IMAGE_NAME")
                        appImage.push()
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                
                echo 'Déploiement de l\'application...'
                
            }
        }
    }

    post {
        
        success {
            mail to: 'notif-jenkins@joelkoussawo.me',
                 subject: "Succès du Pipeline: ${env.JOB_NAME}",
                 body: "Le pipeline ${env.JOB_NAME} a réussi."
        }
        failure {
            mail to: 'notif-jenkins@joelkoussawo.me',
                 subject: "Échec du Pipeline: ${env.JOB_NAME}",
                 body: "Le pipeline ${env.JOB_NAME} a échoué."
        }
    }
}

