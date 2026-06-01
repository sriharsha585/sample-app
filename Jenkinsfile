pipeline {
agent any

environment {
    IMAGE = "nandam585/nginx"
    GITOPS_REPO = "https://github.com/sriharsha585/gitops-demo.git"
}

stages {

    stage('Debug') {
        steps {
            sh '''
            echo "Current Workspace:"
            pwd
            ls -la
            '''
        }
    }

    stage('Build') {
        steps {
            sh '''
            docker build -t $IMAGE:$BUILD_NUMBER .
            '''
        }
    }

    stage('Docker Login & Push') {
        steps {

            withCredentials([
                usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )
            ]) {

                sh '''
                echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin

                docker push $IMAGE:$BUILD_NUMBER
                '''
            }
        }
    }

    stage('Update GitOps Repo') {
        steps {

            withCredentials([
                string(
                    credentialsId: 'github-token',
                    variable: 'github-token'
                )
            ]) {

                sh '''
                rm -rf gitops-demo

                git clone https://$github-token@github.com/sriharsha585/gitops-demo.git

                cd gitops-demo/

                sed -i "s|image:.*|image: $IMAGE:$BUILD_NUMBER|g" deployment.yaml

                echo "Updated deployment.yaml"

                cat deployment.yaml

                git config user.email "nandam585@gmail.com"
                git config user.name "nandam585"

                git add deployment.yaml

                git commit -m "Updated image to $BUILD_NUMBER" || true

                git push origin main
                '''
            }
        }
    }
}

post {

    success {
        echo 'Pipeline completed successfully'
    }

    failure {
        echo 'Pipeline failed'
    }

    always {
        sh 'docker images | head'
    }
}
}
