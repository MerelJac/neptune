def dockerImage
def enableDeploy
pipeline {
    agent {
        docker { image 'node:20-alpine' }
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
                    apk add zip curl
                    node --version
                    npm --version
                    npm install
                    npm run build
                    cd ./dist
                    zip -r ../dist.zip ./
                    cd ..
                    ls 
                '''
            }
        }
        stage('push') {
            steps {
                script {
                    def artUser
                    def artPass
                    withCredentials([usernamePassword(credentialsId: 'art_creds', usernameVariable: 'artUsername', passwordVariable: 'artPassword')]) {
                        artUser = artUsername
                        artPass = artPassword
                    }
                    sh """
                        curl -u${artUser}:${artPass} -T ./dist.zip "https://jfrog.mjs.dops.stairways.ai/artifactory/blob-repository/neptune-${BUILD_NUMBER}.zip"
                    """
                }
            }
        }
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