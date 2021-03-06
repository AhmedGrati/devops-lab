pipeline{
    environment {
        registry = "ahmedgrati/sahti"
        registryCredential = 'dockerhub'
        dockerImage = ''
        POSTGRES_CRED = credentials('postgres-credentials')
    }
   agent any
   tools {
       terraform 'Terraform'
   }
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
            stage('Deploy Approval') {
                steps {
                    script {
                        input 'Are you sure about deploying to prod ?'
                    }
                }
            }
            stage('Deploy Tools Verification') {
                steps {
                    script {
                        sh 'terraform --version'
                    }
                }
            }
            stage('Checkout Infrastructure Code') {
                steps {
                    git branch: 'master', url: 'https://github.com/AhmedGrati/sahti-iac'
                }
            }
            stage('Deploy Infrastructure') {
                steps {
                    script {
                        withAWS(credentials: 'aws-credentials', region: 'us-east-1') {
                            sh 'terraform init && terraform apply -var postgres_password=$POSTGRES_CRED_PSW -var postgres_username=$POSTGRES_CRED_USR -auto-approve'
                        }
                    }
                }
            }
            stage('Destroy Approval') {
                steps {
                    script {
                        input 'Are you sure about Destroying Your Infrastructure ?'
                    }
                }
            }
            stage('Destroy Infrastructure') {
                steps {
                    script {
                        withAWS(credentials: 'aws-credentials', region: 'us-east-1') {
                            sh 'terraform init && terraform destroy -var postgres_password=$POSTGRES_CRED_PSW -var postgres_username=$POSTGRES_CRED_USR -auto-approve'
                        }
                    }
                }
            }
            // stage('Deploy of Docker Containers') {
            //     steps{
            //         script {
            //             dir('./unit-int-tests/sahti-backend') {
            //                 sh 'docker-compose down && rm .env'
            //                 withCredentials([file(credentialsId: 'SAHTI_ENV', variable: 'env_file')]) {
            //                     sh "cp \$env_file ."
            //                 }
            //                 sh 'docker-compose up -d'
            //             }
            //         }
            //     }
            // }
    }
    post {
        success {
            emailext (
                  subject: "Pipeline Result!",
                  body: "The pipeline was successful and your product is on now . Check it out on http://http://34.125.74.246:8080/",
                  to: "ahmedgrati1999@gmail.com"
                )
        }
    }
}
