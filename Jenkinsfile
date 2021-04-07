pipeline {
    agent {
        node {
            label 'maven'
        }
    }
    parameters {
            string(name:'PROJECT_VERSION',defaultValue: 'v0.01',description:'')
            string(name:'PROJECT_NAME',defaultValue: '',description:'')
    }
    environment {
        DOCKER_CREDENTIAL_ID = 'dockerhub-id'
        GITHUB_CREDENTIAL_ID = 'github-id'
        KUBECONFIG_CREDENTIAL_ID = 'demo-kubeconfig'
        REGISTRY = 'docker.io'
        DOCKERHUB_NAMESPACE = 'baiyinnamula'
        GITHUB_ACCOUNT = 'baiyinnamula'
        SONAR_CREDENTIAL_ID = 'sonar-token'
    }
    stages {
        stage('拉取代码') {
            steps {
              git(url: 'https://github.com/baiyinnamula/gulimall-learning.git', credentialsId: $GITHUB_CREDENTIAL_ID, branch: 'master', changelog: true, poll: false)
              sh 'echo 正在构建项目： $PROJECT_NAME， 版本号：$PROJECT_VERSION'
          }
        }
        stage('sonarqube analysis') {
            steps {
                container ('maven') {
                    withCredentials([string(credentialsId: "$SONAR_CREDENTIAL_ID", variable: 'SONAR_TOKEN')]) {
                        withSonarQubeEnv('sonar') {
                            sh "mvn sonar:sonar -o -gs `pwd`/mvn-settings.xml -Dsonar.branch=$BRANCH_NAME -Dsonar.login=$SONAR_TOKEN"
                        }
                    }
                    timeout(time: 1, unit: 'HOURS') {
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
        }
    }

}