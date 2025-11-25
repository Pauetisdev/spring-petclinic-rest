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
                cleanWs()
                // CORRECCIÓN 1: Usamos TU repo para que detecte tus 219 tests
                git url: 'https://github.com/Pauetisdev/spring-petclinic-rest.git', branch: 'master'
            }
        }
        
        stage('2. Build, Test & Coverage') {
            steps {
                // CORRECCIÓN 2: No usamos 'dir' porque Jenkins ya está en la carpeta correcta
                sh "mvn clean verify -DskipITs=true"
                
                // Guardamos los resultados de los tests JUnit
                junit 'target/surefire-reports/*.xml' 
                
                // CORRECCIÓN 3 (LA IMPORTANTE): Usamos /** para guardar el HTML y ver la UI
                archiveArtifacts artifacts: 'target/site/jacoco/**', allowEmptyArchive: true
            }
        }
        
        stage('3. SonarQube Scan') {
            environment {
                SONAR_TOKEN = credentials('sonar-token') 
            }
            steps {
                withSonarQubeEnv('LocalSonar') {
                   // Ejecutamos el análisis de Sonar
                    sh "mvn org.sonarsource.scanner.maven:sonar-maven-plugin:4.0.0.4121:sonar -Dsonar.organization= -Dsonar.projectKey=petclinic-backend -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml -Dsonar.scm.disabled=true"
                }
            }
        }

        stage('4. Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true 
                }
            }
        }
    }
}