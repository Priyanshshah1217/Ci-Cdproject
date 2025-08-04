pipeline{
    agent any
    tools {
        maven "MAVEN3.9"
        jdk "JDK21" 

    }

    environment {
        registryCredential = 'ecr:us-east-1:awscreds'
        imageName = '019361055266.dkr.ecr.us-east-1.amazonaws.com/vprofileappimg'
        vprofileRegistry = 'https://019361055266.dkr.ecr.us-east-1.amazonaws.com'
        cluster = "vprofile"
        service = "vprofileappsvc"
    }

    stages {
        stage('Fetch code'){
            steps{
                git branch: 'main', url: 'https://github.com/Priyanshshah1217/Ci-Cdproject.git'
            }
        }

        stage('Build'){
            steps{
                sh 'mvn install -DskipTests'
            }
            post {
                success{
                    echo "Archiving Artifacts"
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }

        stage('Unit Test'){
            steps{
                sh 'mvn test'
            }
        }

        stage('Checkstyle Analysis'){
            steps{
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('Sonar Code Analysis'){
            environment{
                scannerHome = tool 'sonar6.2'
            }
            steps{
                withSonarQubeEnv('Sonarserver') {
                    sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                       -Dsonar.projectName=vprofile \
                       -Dsonar.projectVersion=1.0 \
                       -Dsonar.sources=src/ \
                       -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                       -Dsonar.junit.reportsPath=target/surefire-reports/ \
                       -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                       -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                }

            }
        }   

        stage('Quality Gate'){
            steps {
                timeout(time: 1, unit: 'HOURS'){
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('BuildAppImage'){
            steps {
                script {
                    dockerImage = docker.build( imageName + ":$BUILD_NUMBER", "-f Docker-files/app/multistage/Dockerfile .")
                }
                
            }
        }

        stage("Upload App Image"){
            steps {
                script {
                    docker.withRegistry( vprofileRegistry, registryCredential) {
                        dockerImage.push("$BUILD_NUMBER")
                        dockerImage.push('latest')                        
                    }
                }
            }
        }

        stage('Remove Container Image'){
            steps{
                sh 'docker rmi -f $(docker images -a -q)'
            }
        }

        stage("Deploy to ECS"){
            steps {
                withAWS(credentials: 'awscreds', region: 'us-east-1'){
                    sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
                }
            }
        }
    }
}