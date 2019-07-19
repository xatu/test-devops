node {

    properties([
        parameters([
            gitParameter(
                branch: '', 
                branchFilter: '.*', 
                defaultValue: 'master', 
                description: '', 
                name: 'BRANCH', 
                quickFilterEnabled: false, 
                selectedValue: 'NONE', 
                sortMode: 'NONE', 
                tagFilter: '*', 
                type: 'PT_BRANCH'
            )
        ])
    ])
    
    def branch = "${env.BRANCH}".split('/')[1]

    stage('scm'){
        git branch: "${branch}", url: 'https://gitlab.com/codilabs/Ear-Test.git'
    }

    stage('Show files'){
        sh 'ls -ltrha'
    }
    
    stage('Sonnarqube') {
        withSonarQubeEnv(credentialsId: 'sonar-token') {
            def scan = tool name: 'scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
            writeFile file: 'sonar-project.properties', text: '''sonar.host.url=http://sonarqube:9000
            sonar.projectKey=test
            sonar.projectName=Ear-Test
            sonar.projectVersion=>0.0.1-SNAPSHOT
            sonar.sources=.
            sonar.sourceEncoding=UTF-8
            sonar.exclusions=**/target/**
            sonar.java.source=1.8
            '''            
            sh "${scan}/bin/sonar-scanner"
        }
    }

    stage("Quality Check"){
        timeout(time: 20, unit: 'MINUTES') {
            def qg = waitForQualityGate()
            if (qg.status == 'WARN') {
                currentBuild.result = 'UNSTABLE'
            }
        }
    }

    stage('Build') {
        def maven = tool name: 'maven', type: 'maven'
        sh """
            cd testWAR
            ${maven}/bin/mvn clean package
            ls -ltrh
            cd target
            ls -ltrh
        """
    }

    stage('Deploy WAS') {

    }
}