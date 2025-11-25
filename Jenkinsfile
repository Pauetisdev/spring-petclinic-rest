// Jenkinsfile para el proyecto Spring PetClinic REST (Maven)
pipeline {
    agent any 
    
    tools {
        maven 'Maven-3.9'
        jdk 'JDK-17'
    }

    stages {
        stage('1. Checkout Code') {
            steps {
                // FIX CRÍTICO: Descarga el código del repositorio.
                // ATENCIÓN: Si hiciste un fork, reemplaza la URL de Git con la tuya.
                git url: 'https://github.com/spring-petclinic/spring-petclinic-rest.git', branch: 'main'
            }
        }
        
        stage('2. Build, Test & Coverage') {
            steps {
                // FIX CRÍTICO: Entramos en la carpeta del repositorio para encontrar el pom.xml
                dir('spring-petclinic-rest') {
                    sh "mvn clean verify -DskipITs=true"
                    junit 'target/surefire-reports/*.xml' 
                    archiveArtifacts artifacts: 'target/site/jacoco/jacoco.xml, target/surefire-reports/*.xml', onlyIfSuccessful: true
                }
            }
        }
        
        stage('3. SonarQube Scan & Quality Gate') {
            environment {
                SONAR_TOKEN = credentials('sonar-token') 
            }
            steps {
                // FIX: Entramos en la carpeta para ejecutar el scan
                dir('spring-petclinic-rest') {
                    withSonarQubeEnv('LocalSonar') {
                        sh "mvn sonar:sonar -Dsonar.organization= -Dsonar.projectKey=petclinic-backend -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml -Dsonar.scm.disabled=true"
                    }
                    timeout(time: 5, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true 
                    }
                }
            }
        }
    }
}