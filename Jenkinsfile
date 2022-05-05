pipeline{
    environment {
        registry = "ahmedgrati/sahti"
        registryCredential = 'dockerhub'
        dockerImage = ''
    }
    agent any
   stages{
         stage('SCM Checkout'){
                steps {
                    git branch: 'master', url: 'https://github.com/AhmedGrati/devops-lab'
                }
            }
            stage('Build NPM Project'){
                steps{
                    script {
                        nodejs('Node-16.13') {
                            sh "cd ./unit-int-tests/sahti-backend && npm cache clean -f && rm -rf node_modules && npm i --legacy-peer-deps && npm run build"
                        }
                    }
                }
            }
            stage('Verify Tools') {
                steps {
                    script {
                        sh '''
                            docker --version
                            docker info
                            docker-compose --version
                        '''
                    }
                }
            }
            stage('Test NPM Project') {
                steps{
                    script {
                        nodejs('Node-16.13') {
                            sh "cd ./unit-int-tests/sahti-backend && npm i --legacy-peer-deps && npm run test && npm run test:e2e"
                        }
                    }
                }
            }
            stage('Build Docker Image') {
                steps{
                    dir('./unit-int-tests/sahti-backend') {
                        script {
                            dockerImage = docker.build registry + ":latest"
                        }
                    }
                }
            }
            stage('Push Docker Image To DockerHub Registry') {
                steps{
                    script {
                        docker.withRegistry( '', registryCredential ) {
                            dockerImage.push()
                        }
                    }
                }
            }
            stage('Approval') {
                steps {
                    script {
                        input 'Are you sure about deploying to prod ?'
                    }
                }
            }
            stage('Deploy of Docker Containers') {
                steps{
                    script {
                        dir('./unit-int-tests/sahti-backend') {
                            sh 'docker-compose down && rm .env'
                            withCredentials([file(credentialsId: 'SAHTI_ENV', variable: 'env_file')]) {
                                sh "cp \$env_file ."
                            }
                            sh 'docker-compose up -d'
                        }
                    }
                }
            }
    }
    post {
        success {
            mail to: ahmedgrati1999@gmail.com
        }
    }
//             stage('Push Docker Image') {
//                 steps {
//                     script {
//                         withCredentials([string(credentialsId: 'docker-new-pwd', variable: 'dockerHubNewPwd')]) {

//                             sh "sudo -n docker login -u wassalni -p ${dockerHubNewPwd}"

//                         }

//                         sh 'sudo -n docker push wassalni/wassalnibackend:1.0.0'
//                     }

//                 }
//             }
//             stage('Release'){
//                 steps {
//                     script {
//                          def upCommand = "sudo docker-compose -f /home/ubuntu/wassalni/wasalni-docker/docker-compose.yml up -d"
//                          sh "${upCommand}"
//                     }

//                 }
//             }
//    }
//     post {
//         success {
//               emailext (
//                   subject: "Build Log !",
//                   body: "The build was successful  and your product is on now . Check it out on http://3.234.221.82:8080/",
//                   recipientProviders: [[$class: 'DevelopersRecipientProvider']]
//                 )
//         }
//         failure {
//               emailext (
//                   attachLog:true,
//                   subject: "Build Log ",
//                   body: "The build failed and your product is not on production now . To Debug it check out the last build on http://3.234.221.82:9000/job/WassalniCICD",
//                   recipientProviders: [[$class: 'DevelopersRecipientProvider']]
//                 )
//         }
//     }

}
