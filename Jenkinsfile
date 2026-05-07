pipeline {
    // This tells Jenkins it can run on any available server node
    agent any
    
    stages {
        stage('Checkout Code') {
            steps {
                // This automatically pulls the latest code from your GitHub main branch
                checkout scm
            }
        }
        
        stage('Train Model') {
            steps {
                // Installs requirements and runs your training script
                // We keep the --break-system-packages flag for your Ubuntu environment
                sh '''
                echo "Installing dependencies..."
                pip3 install pandas scikit-learn fastapi uvicorn --break-system-packages
                
                echo "Training the machine learning model..."
                python3 train.py
                '''
            }
        }
        
        stage('Deploy API via Docker') {
            steps {
                // Cleans up the old container, builds the new one, and runs it
                sh '''
                echo "Stopping and removing old container (if it exists)..."
                docker stop ml-api || true
                docker rm ml-api || true
                
                echo "Building new Docker image..."
                docker build -t ml-api-image .
                
                echo "Running new container on Port 8000..."
                docker run -d --name ml-api -p 8000:8000 ml-api-image
                '''
            }
        }
    }
    
    post {
        // This section will run after the stages finish
        success {
            echo "Pipeline completed successfully! API is live on port 8000."
        }
        failure {
            echo "Pipeline failed. Please check the Jenkins logs."
        }
    }
}
