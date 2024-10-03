def dockerImage
def enableDeploy
pipeline {
    agent {
        docker { image 'node:20-alpine3' }
    }
    stages {
        stage('setup') {
            steps {
                script {
                    properties([
                            parameters([
                                // [$class: 'ChoiceParameter', 
                                //     choiceType: 'PT_SINGLE_SELECT', 
                                //     description: 'Force manual deployment',
                                //     name: 'FORCE_DEPLOY', 
                                // ],
                                booleanParam(
                                    defaultValue: false,
                                    description: 'Force manual deployment', 
                                    name: 'FORCE_DEPLOY'
                                ),
                            ])
                        ])
                }
            }
        }
        stage('build') {
            steps {
                sh '''
                    node --version
                    npm --version
                    npm run build
                    cd ./dist
                    zip ../dist.zip ./
                    cd ..
                    ls
                '''
            }
        }
        // stage('push') {
        //     steps {
        //         script {
        //             docker.withRegistry("https://artifactory.mjs.dops.stairways.ai", "art_creds") {
        //                 dockerImage.push("latest")      
        //             }
        //         }
        //     }
        // }
        // stage('deploy') {
        //     when { expression { params.FORCE_DEPLOY == true || env.BRANCH_NAME == 'master' } }
        //     steps {
        //         script {

        //             def artUser
        //             def artPass
        //             withCredentials([usernamePassword(credentialsId: 'art_creds', usernameVariable: 'artUsername', passwordVariable: 'artPassword')]) {
        //                 artUser = artUsername
        //                 artPass = artPassword
        //             }

        //             withCredentials([sshUserPrivateKey(credentialsId: 'jsu-ssh-creds', keyFileVariable: 'privateKey', passphraseVariable: 'keyPass', usernameVariable: 'userName')]) {
        //                 def remote = [:]
        //                 remote.name = "debian-test-droplet-sfo03-01"
        //                 remote.host = "147.182.253.167"
        //                 remote.allowAnyHosts = true
        //                 remote.user = userName
        //                 remote.passphrase = keyPass
        //                 remote.identityFile = privateKey
        //                 sshCommand remote: remote, command: '''
        //                     curl http://169.254.169.254/metadata/v1/id
        //                     cd /usr/docker
        //                     docker login https://artifactory.mjs.dops.stairways.ai --username ${artUser} --password ${artPass}
        //                     docker compose pull
        //                     docker compose up -d
        //                 '''
        //             }
        //         }
        //     }
        // }
    }
}