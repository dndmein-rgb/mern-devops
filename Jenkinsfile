pipeline {
    agent any

    environment {
        FRONTEND_IMAGE = "mern-frontend:jenkins"
        BACKEND_IMAGE  = "mern-backend:jenkins"
        PORT = "5000"
        MONGO_URI = "mongodb://mongo:27017/taskdb"
    }

    stages {
        stage('Prepare .env') {
            steps {
                sh '''
                    mkdir -p server
                    cat > server/.env <<EOF
PORT=$PORT
MONGO_URI=$MONGO_URI
EOF
                '''
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                    echo "Building backend image..."
                    docker build -t $BACKEND_IMAGE ./server

                    echo "Building frontend image..."
                    docker build \
                      -t $FRONTEND_IMAGE \
                      ./client \
                      --build-arg VITE_API_URL=http://localhost:5000/api
                '''
            }
        }

        stage('Run with Docker Compose') {
            steps {
                sh '''
                    echo "Stopping old containers..."
                    docker compose down || true

                    echo "Starting MERN stack..."
                    docker compose up -d

                    echo "Running containers..."
                    docker ps

                    echo "===== Backend Logs ====="
                    docker logs backend || true

                    echo "===== Frontend Logs ====="
                    docker logs frontend || true
                '''
            }
        }
    }
}