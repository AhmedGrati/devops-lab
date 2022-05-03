pipeline{

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
            stage('Test NPM Project') {
                steps{
                    script {
                        nodejs('Node-16.13') {
                            sh "cd ./unit-int-tests/sahti-backend && npm i --legacy-peer-deps && npm run test"
                        }
                    }
                }
            }
            stage('Verify Tools') {
                steps {
                    script {
                        sh '''
                            sudo docker --version
                            sudo docker info
                            sudo docker-compose --version
                        '''
                    }
                }
            }
            // stage('Build Docker Image') {
            //     steps{
            //         script {
            //             sh 'sudo -n docker build -t wassalni/wassalnibackend:1.0.0 .'
            //         }
            //     }
            // }
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