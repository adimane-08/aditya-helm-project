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

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/adimane-08/aditya-helm-project.git', credentialsId: 'code-for-k8s'
            }
        }

        stage('Build Image') {
            steps {
                dir('app') {
                    bat 'dir'
                    bat 'docker build -f Dockerfile -t  %DOCKERHUB%/%IMAGE%:%BUILD_NUMBER% .'
                }
            }
        }

        stage('Push Image') {
            steps {
                 withDockerRegistry([ credentialsId: 'token-for-kubernetes-repo', url: '' ]) {
                    bat 'docker push %DOCKERHUB%/%IMAGE%:%BUILD_NUMBER%'
                }
            }
        }
        stage('Deploy via Helm') {
            steps {
                script {
                    // Define namespace mapping here
                    def NAMESPACE_MAP = [dev:'dec', test:'test', prod:'prod']
        
                    // Select namespace based on ENV parameter
                    def ns = NAMESPACE_MAP[params.ENV]
        
                    // Create namespace if it doesn't exist
                    bat "kubectl get ns ${ns} || kubectl create ns ${ns}"
        
                    // Helm deploy
                    bat """
                    helm upgrade --install aditya-app ./helm/aditya-app ^
                    -f helm/aditya-app/values-${params.ENV}.yaml ^
                    --set image.tag=%BUILD_NUMBER% ^
                    --namespace ${ns}
                    """
        }
    }
}
    }
    }
}


