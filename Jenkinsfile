pipeline {
    agent any

    environment {
        app_name = 'trading-app'

        //Init
        AZ_USER=credentials('AZ_USER')
        AZ_PWD=credentials('AZ_PWD')
        AZ_TENANT=credentials('AZ_TENANT')

        //Build
        ACR_NAME='acrJarvisKevinShimotakahara2'
        //${BUILD_NUMBER} is a Jenkins built-in env https://bit.ly/32mySlQ
        //Note: double quote is neccessary here
        IMAGE_NAME="springboot_demo_app:${BUILD_NUMBER}"

        //Deploy
        RESOURCE_GROUP='rg-trading-app-aks'
        CLUSTER_NAME='aks-trading-app'
        DOCKER_REPO='acrjarviskevinshimotakahara2.azurecr.io'
        DEPLOYMENT_NAME='deployment.apps/springboot-demo-app-dev'
    }

    stages {
        stage('Init') {
            steps {
                sh 'az login --service-principal --username ${AZ_USER} --password ${AZ_PWD} --tenant ${AZ_TENANT}'
            }
        }
        stage('Build') {
            steps {
                sh ' az acr build --image ${IMAGE_NAME} --registry $ACR_NAME --file Dockerfile .'
            }
        }
        stage('Deploy') {
            steps {
                //Connect to AKS
                sh 'az aks get-credentials --name $CLUSTER_NAME --resource-group $RESOURCE_GROUP'
                //show cluster info for debugging purposes
                sh 'kubectl cluster-info'
                //Deploy the new image
                sh 'kubectl set image ${DEPLOYMENT_NAME} springboot-demo-app=${DOCKER_REPO}/${IMAGE_NAME}'
                //Check result
                sh 'kubectl get all'
            }
        }
    }
}
