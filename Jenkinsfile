// pipeline {
//     agent any
//     environment {
//         DOCKER_IMAGE_NAME = "power-app"
//     }
//     stages{
//         stage('Clone repository') {
//         /* Let's make sure we have the repository cloned to our workspace */
//             steps{
//                 checkout scm
//                 echo 'Pulling...' + env.BRANCH_NAME
//                 updateGitlabCommitStatus name: 'build', state: 'pending'
//             }
//         }

//         stage('Build Docker Image') {
//             steps {
//                 echo "GIT BRANCH is ${GIT_BRANCH}"
//                 script {
//                     app = docker.build(DOCKER_IMAGE_NAME)
//                     app.inside {
//                         sh 'echo "Tests passed"'
//                     }
//                 }
//             }
//         }
//         stage('Push Docker Image To Repo') {
//             steps {
//                 script {
//                     if ( GIT_BRANCH == "origin/master") {
//                         docker.withRegistry('https://docker.abujaelectricity.com') {
//                         app.push("latest")
//                       }
//                     }
//                     else {
//                         docker.withRegistry('https://docker.abujaelectricity.com') {
//                         app.push("test")
//                       }
//                     }
//                 }
//             }
//         }
//     }
//     post {
//       failure {
//         updateGitlabCommitStatus name: 'build', state: 'failed'
//       }
//       success {
//         updateGitlabCommitStatus name: 'build', state: 'success'
//       }
//     }
// }

pipeline {
    agent any 
    environment {
        registry = "https://172.24.5.45:5000/powerappz"
        // registry = "https://docker.abujaelectricity.com/powerappz"
        app = ''
        imagename = 'powerappz'
    }
    stages {

        stage("checkout repo") {
            steps {
                echo "cloning git repository"
                git credentialsId: 'gitlab', url: 'https://Paul.Braimoh@git.abujaelectricity.com/Uswat.Abdulazeez/power-app.git'
            }
        }

        stage("Build dockerfile") {
            steps {
                echo "Building dockerfile"
                script {
                    app = docker.build imagename + ":$BUILD_NUMBER"
                }
            }
        }
        stage("Push to docker repo") {
            steps {
                script{
                    docker.withRegistry( "https://172.24.5.45:5000" ) {
                        app.push()
                        // app.push('latest')
                        // app.push("$BUILD_NUMBER")
                        // app.push('latest')
                    }
                }
            }
        }
        // stage('Remove Unused docker image') {
        //     steps{
        //         sh "docker rmi $imagename"
        //     }
        // }
        stage("Deploy app") {
            steps {
                script {
                    kubernetesDeploy(configs: "k8s.yml", kubeconfigId: "mykubeconfig")
                }
            }
        }
    }
}