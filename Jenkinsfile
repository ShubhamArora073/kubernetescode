node {
    def app
    environment{
        DOCKERHUB_CREDS = credentials('DockerHub')
    }

    stage('Clone repository') {
      

        checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ShubhamArora073/kubernetescode.git']])
    }

    stage('Build image') {
  
       app = docker.build("shubhamarora073/test")
    }

    stage('Test image') {
  

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Docker Login') {
            steps {
                //sh 'docker login -u $DOCKERHUB_CREDS_USR -p $DOCKERHUB_CREDS_PSW' (this will leave the password visible)
                sh 'echo $DOCKERHUB_CREDS_PSW | sudo docker login -u $DOCKERHUB_CREDS_USR --password-stdin'                
                }
            }

    stage('Push image') {
        
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            app.push("${env.BUILD_NUMBER}")
        }
    }
    
    stage('Trigger ManifestUpdate') {
                echo "triggering updatemanifestjob"
                build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
        }
}

// node {
//     def app

//     stage('Clone repository') {
//         checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ShubhamArora073/kubernetescode.git']])
//     }

//     stage('Build image') {
//         app = sudoDockerBuild("shubhamarora073/test")
//     }

//     stage('Test image') {
//         app.inside {
//             sh 'echo "Tests passed"'
//         }
//     }

//     stage('Push image') {
//         sudoDockerWithRegistry('https://registry.hub.docker.com', 'dockerhub') {
//             app.push("${env.BUILD_NUMBER}")
//         }
//     }

//     stage('Trigger ManifestUpdate') {
//         echo "triggering updatemanifestjob"
//         build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
//     }
// }

// def sudoDockerBuild(imageName) {
//     script {
//         sudo("docker build -t ${imageName} .")
//         return docker.image(imageName)
//     }
// }

// def sudoDockerWithRegistry(registry, credentialsId, closure) {
//     script {
//         sudo("docker login -u _json_key -p \$(cat \${DOCKERHUB_CREDENTIALS_JSON} | base64 -w 0) https://${registry}")
//         try {
//             closure()
//         } finally {
//             sudo('docker logout')
//         }
//     }
// }

// def sudo(command) {
//     sh "sudo ${command}"
// }
