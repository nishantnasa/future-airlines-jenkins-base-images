#!/usr/bin/env groovy

properties(
  [buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '30', numToKeepStr: '50')), [$class: 'RebuildSettings', autoRebuild: false, rebuildDisabled: false], pipelineTriggers([])]
)

@NonCPS
def readDir()
{
    def  dirsl = []
    new File(workspace + "/images").eachDir()
    {
        dirs -> println dirs.getName()
        if (!dirs.getName().startsWith('.')) {
            dirsl.add(dirs.getName())
        }
    }
    dirsl.sort()
}

def ECR_URI = "762508870528.dkr.ecr.ap-southeast-2.amazonaws.com"
def ECR_REPO_NAME = "future-airlines-jenkins-base-images"

stage('Start build') {
    timeout(time: 1, unit: 'DAYS') {
        input message: 'Click OK to pick your image', ok: 'OK'
    }
}

node {

    DOCKER_REPO = "future-airlines-jenkins-base-images"
    ECR_URI = "762508870528.dkr.ecr.ap-southeast-2.amazonaws.com"

    // Clean workspace
    stage('Clean workspace') {
        cleanWs()
    }

    // Check out the repository
    stage('Checkout') {
        checkout scm
    }

    stage('Choose your image to build') {
        def user_input_build_image = input message: 'Choose the image to build', parameters: [choice(choices: readDir(), description: '', name: 'build_image')]

        // Stash var
        sh "echo ${user_input_build_image} > var_user_input_build_image"
        stash includes: "var_*", name: "var-stash"
    }

    stage('Build Docker image') {
        // Unstash var
        unstash "var-stash"
        def BUILD_IMAGE = readFile('var_user_input_build_image').trim()

        dir("images/${BUILD_IMAGE}") {
            sh "docker build -t ${ECR_URI}/${ECR_REPO_NAME}:${BUILD_IMAGE} ."
        }
    }

    stage('Push Docker image') {
        // Unstash var
        unstash "var-stash"
        def BUILD_IMAGE = readFile('var_user_input_build_image').trim()

        dir("images/${BUILD_IMAGE}") {
            sh "docker push ${ECR_URI}/${ECR_REPO_NAME}:${BUILD_IMAGE}"
        }
    }
}
