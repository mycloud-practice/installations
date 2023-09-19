pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "M3"
    }

    stages {

        // stage('Checkout') {
        //     steps {
        //         // Get some code from a GitHub repository
        //         git branch: 'main',
        //             url: 'https://gitlab.com/Krishna_07/pipelines-java-test.git'
        //     }
        // }
        stage('Checkout') {
            steps {
                // Get some code from a GitHub repository
                checkout scm    
        }
        }
        
         
        stage('Build') {
            
            steps {

                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

        }

        stage('Install in Dev') {
            when {
                branch 'development'
            }
            steps{

                sh "mvn install"
            }
        }

        stage('Deploy from production') {
            when{
                branch 'main'
            }
            steps {
                echo "deploy stage"
                deploy adapters: [tomcat9 (
                    credentialsId: 'tom-admin',
                    path: '',
                    url: 'http://172.172.230.147:8088/'
                )],
                contextPath: 'servlet00',
                onFailure: 'false',
                war: '**/*.war'
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.war'
                }
            }
        }
    }
}
