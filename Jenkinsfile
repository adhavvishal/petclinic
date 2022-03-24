node {
    def mvnHome = tool name: 'Maven_3', type: 'maven'
    def mvnCli = "${mvnHome}/bin/mvn"

    properties([
        buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')),
        disableConcurrentBuilds(),
        [$class: 'GithubProjectProperty', displayName: '', projectUrlStr: 'https://github.com/gouthamchilakala/PetClinic.git/'],
        [$class: 'ThrottleJobProperty', categories: [], limitOneJobWithMatchingParams: false, maxConcurrentPerNode: 0, maxConcurrentTotal: 0, paramsToUseForLimit: '', throttleEnabled: true, throttleOption: 'project'],
        pipelineTriggers([githubPush()]),
        parameters([string(defaultValue: 'DEV', description: 'env name', name: 'environment', trim: false)])
    ])
    stage('Checkout SCM'){
        git 'https://github.com/adhavvishal/petclinic'
    }
    
    stage('maven compile'){
        sh "${mvnCli} clean compile"
    }
    
    stage('maven package'){
        sh "${mvnCli} package -Dmaven.test.skip=true"
    }
    
    stage('Archive atifacts'){
        archiveArtifacts artifacts: '**/*.war', onlyIfSuccessful: true
    }
    
    stage('Archive Test Results'){
        junit allowEmptyResults: true, testResults: '**/surefire-reports/*.xml'
    }
    
    stage('Deploy To Tomcat'){
        sshagent(['deploy-war']) {
            sh 'scp -o StrictHostKeyChecking=no target/*.war ec2-user@http://54.144.94.207:8080:/opt/apache-tomcat-9.0.60/webapps/'
        }
    }
    
    stage('Smoke Test'){
        sleep 5
        sh "curl 54.144.94.207:8080/petclinic"
    }

}
