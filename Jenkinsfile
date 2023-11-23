pipeline{
 environment {
        dockerUserName="victorialloyd"
        credentialsIdGCP = "gcp-cred"
        namespace = "lbg-trainer"
        // e.g. lbg-1 for learner1, lbg-2 for learner2
        projectId= "lbg-mea-leaders-c10"
        
        imageName = "vatcalc"
        registry = "${dockerUserName}/${imageName}"
        registryCredentials = "dockerhub_id"
        clusterName = "lbg-gke"
        location = "europe-west1"
    }

    agent any
        stages {
               stage('Deploy to GKE') {
                steps{
                    sh "sed -i 's|dockerid/image:latest|${dockerUserName}/${imageName}:${env.BUILD_ID}|g' deployment.yaml"
                    step([$class: 'KubernetesEngineBuilder', 
                    projectId: projectId, 
                    clusterName: clusterName, 
                    location: location, 
                    namespace: namespace,
                    manifestPattern: 'deployment.yaml', 
                    credentialsId: credentialsIdGCP, 
                    verifyDeployments: true])
                }
            }

            stage ("Clean up"){
                steps {
                    script {
                        sh 'docker image prune --all --force --filter "until=48h"'
                           }
                }
            }
        }
}
