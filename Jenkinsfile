pipeline {
agent any

```
environment {
    IMAGE_NAME = "suchi411/python-cicd"
    IMAGE_TAG  = "${BUILD_NUMBER}"
}

stages {

    stage('Checkout') {
        steps {
            git branch: 'main',
                url: 'https://github.com/suchi092/python-cicd-project.git'
        }
    }

    stage('Install Dependencies') {
        steps {
            sh 'pip3 install -r requirements.txt'
        }
    }

    stage('Run Tests') {
        steps {
            sh '/var/lib/jenkins/.local/bin/pytest'
        }
    }

    stage('Docker Login') {
        steps {
            withCredentials([
                usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )
            ]) {
                sh '''
                echo $PASS | docker login -u $USER --password-stdin
                '''
            }
        }
    }

    stage('Build Docker Image') {
        steps {
            sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
        }
    }

    stage('Push Docker Image') {
        steps {
            sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
        }
    }

    stage('Deploy') {
        steps {
            sh '''
            docker stop python-app || true
            docker rm python-app || true

            docker run -d \
            --name python-app \
            -p 5000:5000 \
            $IMAGE_NAME:$IMAGE_TAG
            '''
        }
    }
}

post {
    success {
        echo 'Pipeline executed successfully!'
    }

    failure {
        echo 'Pipeline failed!'
    }
}
```

}
