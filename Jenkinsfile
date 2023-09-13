pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // This step is used to check out your source code repository if needed
                // For this example, we assume you have your Kubernetes manifests in a Git repo
                checkout scm
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Define the Kubernetes deployment configuration
                    def kubectlCommand = """
                        kubectl apply -f path/to/your/hello-world-pod.yaml
                    """
                    
                    // Execute the kubectl command using the Kubernetes credentials configured in Jenkins
                    sh(script: kubectlCommand, returnStatus: true)
                    
                    // Check if the kubectl command was successful
                    if (currentBuild.resultIs('SUCCESS')) {
                        echo "Pod deployed successfully!"
                    } else {
                        error "Failed to deploy pod to Kubernetes."
                    }
                }
            }
        }
    }
}
