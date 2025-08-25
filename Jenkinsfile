node {
  stage('SCM') {
    checkout scm
  }
 
  stage('SonarQube Analysis') {
    // Get branch name reliably even in detached HEAD state
    def branch = bat(
        script: "@echo off && git name-rev --name-only HEAD",
        returnStdout: true
    ).trim()

    // Normalize branch name
    branch = branch.replaceFirst(/^remotes\/origin\//, '')
    branch = branch.replaceFirst(/~.*/, '')

    echo "Normalized branch: ${branch}"

    def scannerHome = tool 'SonarScanner for MSBuild'
    def isProdBranch = branch == 'Production_.Net8.0'
    def sonarEnv = isProdBranch ? 'SonarProd' : 'SonarUAT'
    def projectKey = isProdBranch ? 'Tootris-Production' : 'TOOTRiS-UAT'

    echo "Using SonarQube environment: ${sonarEnv}"
    echo "Project Key: ${projectKey}"
    echo "Branch: ${branch}"
 
    withSonarQubeEnv("${sonarEnv}") {
      bat "dotnet ${scannerHome}\\SonarScanner.MSBuild.dll begin /k:\"${projectKey}\" /d:sonar.scanner.scanAll=false"
      bat "dotnet build"
      bat "dotnet ${scannerHome}\\SonarScanner.MSBuild.dll end"
    }
  }
}
