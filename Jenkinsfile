#!/usr/bin/env groovy

pipeline {
    agent {
        label 'slave-group-release'
    }
    
    parameters {
        choice(choices: 'production\nstaging', description: 'Site ?', name: 'site')
    }

    options {
        timeout(time: 3, unit: 'HOURS')
    }

    stages {
        stage('Prepare') {
            steps {
                // Show the agent name in the build list
                script {
                    // The manager variable requires the Groovy Postbuild plugin
                    manager.addShortText(env.NODE_NAME, "grey", "", "0px", "")
                }
                script {
                    env.MAVEN_HOME = tool('Maven')
                    env.MAVEN_OPTS = "-Xmx800m -XX:+HeapDumpOnOutOfMemoryError"
                    env.JAVA_HOME = tool('Oracle JDK 8')
                }
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Publish') {
            environment {
                PATH = "$MAVEN_HOME:$JAVA_HOME:$PATH"
            }
            steps {
                sh "./bin/publish_${params.site}.sh"
            }
        }        
    }

    post {
        always {
            // Clean
            sh 'git clean -fdx -e "*.hprof" || echo "git clean failed, exit code $?"'
        }
    }
}
