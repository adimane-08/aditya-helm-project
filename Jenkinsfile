pipeline {
    agent any

    parameters {
        choice(name: 'ENV', choices: ['dev', 'test', 'prod'], description: 'Select Environment')
    }

    environment {
        KUBECONFIG = 'C:\\Users\\Aditya\\.kube\\config'
        DOCKERHUB = 'adimane0801'
        IMAGE = 'nginx-probe'
        TAG = '1.29'
        DOCKER_IMAGE = "adimane0801/nginx-probe"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'feature', url: 'https://github.com/adimane-08/aditya-helm-project.git', credentialsId: 'code-for-k8s'
            }
        }

        stage('Build Image') {
            steps {
                dir('app') {
                    bat 'docker build -t  %DOCKERHUB%/%IMAGE%:%BUILD_NUMBER% .'
                }
            }
        }

        stage('Push Image') {
            steps {
                 withDockerRegistry([ credentialsId: 'token-for-kubernetes-repo', url: '' ]) {
                    bat 'docker push $DOCKER_IMAGE:${BUILD_NUMBER}'
                }
            }
        }

        stage('Deploy via Helm') {
            steps {
                bat """
                helm upgrade --install aditya-app ./helm/aditya-app \
                -f helm/aditya-app/values-${params.ENV}.yaml \
                --set image.tag=${BUILD_NUMBER}
                """
            }
        }
    }
}
