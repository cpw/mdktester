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
        MC_VERSION = '1.14.3'
    }

    stages {
        stage('query') {
            steps {
                script {
                    def mdkquery = httpRequest url: 'https://files.minecraftforge.net/maven/net/minecraftforge/forge/promotions_slim.json', consoleLogResponseBody: true
                    env.MDKVERSION = new groovy.json.JsonSlurper().parseText(mdkquery.content)['promos']["${MC_VERSION}-latest"]
                    def mcpquery = httpRequest url: 'http://export.mcpbot.bspk.rs/versions.json', consoleLogResponseBody: true
                    env.MCPVERSION = new groovy.json.JsonSlurper().parseText(mcpquery.content)["${MC_VERSION}"]['snapshot'][0]
                }
                discordSend(
                    title: "Job: MDK ${MDKVERSION} MCP ${MCPVERSION} Build Testing Started",
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
        stage('buildandtest') {
            steps {
                sh './gradlew ${GRADLE_ARGS} --refresh-dependencies -PJENKINS_MC_VERSION=${MC_VERSION} -PJENKINS_MCP_VERSION=${MCP_VERSION} -PJENKINS_FORGE_VERSION=${MDK_VERSION} --continue build test'
            }
            post {
                always {
                    discordSend(
                        title: "Job: MDK ${MDKVERSION} MCP ${MCPVERSION} Build Testing Finished ${currentBuild.currentResult}",
                        successful: currentBuild.resultIsBetterOrEqualTo("SUCCESS"),
                        result: currentBuild.currentResult,
                        thumbnail: JENKINS_HEAD,
                        webhookURL: DISCORD_WEBHOOK
                    )
                }
            }
        }
    }
}