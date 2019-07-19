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
/*
    stage('Clean') {
        cleanWs()
    }
*/
    stage('SCM'){
        git branch: "${branch}", url: 'https://gitlab.com/codilabs/Ear-Test.git'
    }

    stage('Show files'){
        sh 'ls -ltrha'
    }
    
    stage('Sonnarqube') {
        withSonarQubeEnv(credentialsId: 'sonar-token') {
            def scan = tool name: 'scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
            writeFile file: 'sonar-project.properties', text: '''sonar.projectKey=test
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

    stage('Quality gate') {
        timeout(time: 1, unit: 'MINUTES') {
            def qg = waitForQualityGate()
            if (qg.status != 'OK') {
                error "Pipeline failed due to sonarqube: ${qg.status}"
            }
        }
    }


    stage('Build and Deploy') {
        def maven = tool name: 'maven', type: 'maven'
        configFileProvider([configFile(fileId: '68ff4cc8-1127-419c-b691-8ab28323f659', targetLocation: "${env.WORKSPACE}/settings.xml")]) {
            configFileProvider([configFile(fileId: 'a04c3037-4e2a-43bf-8810-23de7fbe56d4', targetLocation: "${env.WORKSPACE}/pom.xml")]) {
                configFileProvider([configFile(fileId: '2661c4b2-0d44-428e-a734-bba0ef32c65c', targetLocation: "${env.WORKSPACE}/was-maven-plugin.properties")]) {
                    sh """
                        ${maven}/bin/mvn clean install -Ddeploy_targets=node01
                    """
                }
            }
        }
    }
}


