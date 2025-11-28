pipeline{
    agent any
    environment{
        SONAR_HOME= tool "sonar"
    }
    stages{
        stage("Clone Code from GitHub") {
            steps{
                git url: "https://github.com/PruthvirajIngale81/devsecops-cicd.git", branch: "main"
            }
        }
        stage("SonarQube Quality Analysis") {
            steps{
                withSonarQubeEnv("sonar"){
                    sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=todo-app -Dsonar.projectKey=todo-app"
                }
            }
        }
        stage("OWASP Dependecy Check") {
            steps{
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'dc'
                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
            }
        }
        stage("Sonar Quality Gate Scan"){
            steps{
                timeout(time: 2, unit: "MINUTES") {
                    waitForQualityGate abortPipeline: false
                }
            }
        }
        stage("Trivy File System Scan") {
            steps{
                 catchError(buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                     sh "trivy fs --format table -o trivy-fs-report.html ."
                 }
            }
        }
        stage("Deploy using Docker Compose"){
            steps{
                sh "docker compose up -d"
            }
        }
    }
}
