import hudson.Util;
pipeline {
    agent any
    stages {
        stage('Deploy') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'VPS_PROJEKT_ZESPOLOWY', passwordVariable: 'password', usernameVariable: 'username')]) {
                        def remote = [:]
                        remote.name = "VPS_PROJEKT_ZESPOLOWY"
                        remote.host = "51.38.131.167"
                        remote.allowAnyHosts = true
                        remote.user = username
                        remote.password = password
                        sshCommand(
                            command: """
                                cd /home/ubuntu/projekt/projekt_zespolowy
                                docker-compose -f docker-compose.dev.yaml down
                                git pull
                                docker-compose -f docker-compose.dev.yaml up -d --build
                                docker-compose -f docker-compose.dev.yaml exec -T back composer install
                                docker-compose -f docker-compose.dev.yaml exec -T front npm install
                            """.stripIndent(), 
                            remote: remote
                        )
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                
                def buildDurationString = Util.getTimeSpanString(currentBuild.duration)
                
                withCredentials([string(credentialsId: 'DISCORD_PROJEKT_ZESPOLOWY', variable: 'secret')]) {
                    discordSend(
                        description: "Deploy NR: ${env.BUILD_NUMBER}\nCzas trwania: ${buildDurationString}\nStatus: ${currentBuild.result}", 
                        footer: '', 
                        image: '', 
                        link: '', 
                        result: '', 
                        thumbnail: '', 
                        title: 'Deploy - Projekt Zespolowy', 
                        webhookURL: "https://discord.com/api/webhooks/${secret}"
                    )
                }
                
            }
        }
    }
}
