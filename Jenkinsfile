pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "maven3"
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Get some code from a GitHub repository
                git branch: params.BRANCHE , url: 'https://github.com/aymendr/maven_with_bugs.git'
            }
        }
        stage('Checkout Code && Build Maven') {
            steps {
                script{
                    switch(params.BRANCHE){
                        case "main": sh "mvn -DskipTests clean package";break
                        case "stage": sh "mvn -DskipTests clean package";break
                    }
                    // Run Maven on a Unix agent.
                }
            }
        }
        /*stage('SonarQube Analysis') {
            steps {
                // Execute SonarQube analysis
                script {
                    withSonarQubeEnv('sonar_server') {
                        sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.7.0.1746:sonar'
                    }
                }
            }
        }*/ 
        stage('SonarQube Analysis') {
            steps {
                // Execute SonarQube analysis
                script {
                    def scannerHome = tool 'sonar_scanner'
                    withSonarQubeEnv('sonar_server') {
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                        -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/"
                    }
                }
            }
        }
        stage("Quality Gate") {
             steps {
                timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
                }
             }
        }
    }
}
