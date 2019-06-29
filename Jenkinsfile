pipeline {
    agent {
        docker {
            image 'gradlewrapper:latest'
            args '-v gradlecache:/gradlecache'
        }
    }
    environment {
        GRADLE_ARGS = '-Dorg.gradle.daemon.idletimeout=5000'
        DISCORD_WEBHOOK = credentials('forge-discord-jenkins-webhook')
        DISCORD_PREFIX = "Job: MDK TESTING Branch: ${BRANCH_NAME} Build: #${BUILD_NUMBER}"
        JENKINS_HEAD = 'https://wiki.jenkins-ci.org/download/attachments/2916393/headshot.png'
    }

    stages {
        stage('query') {
            steps {
                script {
                    def mdkquery = httpRequest 'https://files.minecraftforge.net/maven/net/minecraftforge/forge/promotions_slim.json'
                    env.MDKVERSION = mdkquery.response['promos']['1.14.3-latest']
                }
            }
        }
    }
}