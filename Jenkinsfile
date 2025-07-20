pipeline {
    agent { label 'sonar' }

    stages {
        /*stage('Git Checkout Stage'){
            steps {
                git branch: 'main', url: 'https://github.com/tranju664/Sonar-Qube-war-example.git'
            }
        }*/

        stage('Build Stage') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('SonarQube Analysis Stage') {
            steps {
                withSonarQubeEnv('sonardemo') { 
                    sh "mvn clean verify sonar:sonar -Dsonar.projectKey=sonardemo"
                }
            }
        }
    }

    post {
        success {
            script {
                def server = Artifactory.server 'jfrog'  

                def rtMaven = Artifactory.newMavenBuild()
                rtMaven.tool = 'maven'
                rtMaven.deployer = [
                    server: server,
                    releaseRepo: 'libs-release',
                    snapshotRepo: 'libs-snapshot'
                ]

                rtMaven.run pom: 'pom.xml', goals: 'clean install'
            }
        }
    }
}
