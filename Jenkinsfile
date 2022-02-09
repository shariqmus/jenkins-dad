#!/usr/bin/groovy

def label = "Jenkins-${UUID.randomUUID().toString()}"

podTemplate(label: label, containers: [
    containerTemplate(name: 'jnlp', image: 'jenkins/jnlp-slave:3.40-1-alpine', args: '${computer.jnlpmac} ${computer.name}', workingDir: '/home/jenkins', resourceRequestCpu: '200m', resourceLimitCpu: '300m', resourceRequestMemory: '256Mi', resourceLimitMemory: '512Mi'),
    containerTemplate(name: 'cntr', image: 'ubuntu:latest', workingDir: '/home/jenkins', ttyEnabled: true, command: 'cat') 
],
volumes:[
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
    hostPathVolume(mountPath: '/usr/bin/docker', hostPath: '/usr/bin/docker'),
]) {

  node (label) {
        properties([disableConcurrentBuilds()])
    try {
          def pwd = pwd()
          
          stage ('Clean') {
              deleteDir()
          }

          stage('Checkout') {
                  ansiColor('xterm') {
                  println "Checkout the git repo"
                  checkout scm
              }
          }

        //if (env.BRANCH_NAME =~ 'PR-*' || env.BRANCH_NAME =~ 'SRE-*') 
        {
          container('cntr') {    
            stage ('Call second pipeline') {
              ansiColor('xterm') {
                build job: 'shariq-js/main', parameters: [
                                                          string(name: 'param1', value:'val1')
                                                       ], wait: true
              }
            }
          }
        }
    } 
    catch(error) {
        currentBuild.result = "FAILED"
        throw error
    } 
    finally {
    }
  }
}
