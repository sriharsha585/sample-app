pipeline {

    agent any

    environment {
        IMAGE = "nandam585/nginx"
    }

    stages {

        stage('Debug') {
            steps {
                sh '''
                pwd
                ls -la
                '''
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t $IMAGE:$BUILD_NUMBER .'
            }
        }

        stage('Push') {
            steps {
                sh 'docker push $IMAGE:$BUILD_NUMBER'
            }
        }

        stage('Update GitOps Repo') {
            steps {
                sh '''
                git clone https://github.com/sriharsha585/gitops-demo.git

                cd gitops-demo/nginx

                sed -i "s|image:.*|image: $IMAGE:$BUILD_NUMBER|g" deployment.yaml

                git config user.email "nandam585@gmail.com"
                git config user.name "nandam585"

                git commit -am "Updated image"
                git push
                '''
            }
        }
    }
}
