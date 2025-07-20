pipeline {
    agent { label 'sonar' }

    stages {
        /*stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/tranju664/Sonar-Qube-war-example.git'
            }
        }*/

        stage('SonarQube Analysis Stage') {
            steps {
                withSonarQubeEnv('sonardemo') {
                    sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=sonardemo'
                }
            }
        }

        stage('Build Stage') {
            steps {
                sh 'mvn clean install'
            }
        }
    }

    post {
        success {
            script {
                def server = Artifactory.newServer(
                    url: 'http://13.201.69.137:8081/artifactory/',
                    credentialsId: 'jfrog'
                )

                def rtMaven = Artifactory.newMavenBuild()
                rtMaven.deployer server: server, releaseRepo: 'libs-release/', snapshotRepo: 'libs-snapshot/'
                rtMaven.tool = 'maven'
                rtMaven.run(pom: 'pom.xml', goals: 'clean install')
            }
        }
    }
}
