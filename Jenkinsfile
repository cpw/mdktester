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
        DISCORD_PREFIX = "Job: MDK TESTING Build: #${BUILD_NUMBER}"
        JENKINS_HEAD = 'https://wiki.jenkins-ci.org/download/attachments/2916393/headshot.png'
    }

    stages {
        stage('query') {
            steps {
                script {
                    def mdkquery = httpRequest url: 'https://files.minecraftforge.net/maven/net/minecraftforge/forge/promotions_slim.json', consoleLogResponseBody: true
                    env.MDKVERSION = new groovy.json.JsonSlurper().parseText(mdkquery.content)['promos']['1.14.3-latest']
                    def mcpquery = httpRequest url: 'http://export.mcpbot.bspk.rs/versions.json', consoleLogResponseBody: true
                    env.MCPVERSION = new groovy.json.JsonSlurper().parseText(mcpquery.content)['1.14.3']['snapshot'][0]
                }
                discordSend(
                    title: "Job: MDK and MCP Testing : MDK ${MDKVERSION} MCP ${MCPVERSION} Started",
                    successful: true,
                    result: 'ABORTED', //White border
                    thumbnail: JENKINS_HEAD,
                    webhookURL: DISCORD_WEBHOOK
                )
            }
        }
        stage('fetch') {
            steps {
                checkout scm
            }
        }
    }
}