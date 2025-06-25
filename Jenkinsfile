pipeline{
    agent any
    environment{
        SONAR_HOME= tool "Sonar"
    }
    
    stages{
        stage("code clone from github"){
            steps{
                git url: "https://github.com/abhihkX8/Wanderlust", branch: "dev"
                
            }
        }
        stage("SonarQube Quality Analysis"){
            steps{
                withSonarQubeEnv("Sonar"){
                sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=wanderlust -Dsonar.projectKey=wanderlust"
                
                }
            }
        }
        stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'owasp'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage("SonarQuality Gate Check"){
            steps{
               timeout(time: 2, unit: "MINUTES"){
                   waitForQualityGate abortPipeline: false
               }
            }
        }
        stage("trivy filesystem scan"){
            steps{
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        stage("Deploy using Docker Compose"){
            steps{
                sh "docker compose up -d"
            }
        }
    
    
    }
}
