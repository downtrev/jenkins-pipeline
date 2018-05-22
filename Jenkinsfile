import groovy.json.JsonSlurper

node {
    def container
    def acrSettings

    withCredentials([azureServicePrincipal('principal-credentials-id')]) {
        stage('Prepare Environment') {
            sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID'
            sh 'az account set -s $AZURE_SUBSCRIPTION_ID'
            acrSettings = new JsonSlurper().parseText(
                                            sh(script: "az acs show -o json -n my-acr", returnStdout: true))
        }

        stage('Build') {
            container = docker.build("${acrSettings.loginServer}/my-app:${env.BUILD_ID}")
        }

        stage('Publish') {
            /* https://issues.jenkins-ci.org/browse/JENKINS-46108 */
            sh "docker login -u ${AZURE_CLIENT_ID} -p ${AZURE_CLIENT_SECRET} ${acrSettings.loginServer}"
            container.push()
        }

        stage('Deploy') {
            echo 'Orchestrating a new deployment with kubectl is a simple exercise left to the reader ;)'
        }
    }
}
