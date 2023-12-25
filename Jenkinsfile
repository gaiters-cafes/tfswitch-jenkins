pipeline {
  agent { label 'linux'}
  options {
    skipDefaultCheckout(true)
  }
  stages{
    stage('clean workspace') {
      steps {
        cleanWs()
      }
    }
    stage('checkout') {
      steps {
        checkout scm
      }
    }
    stage('Check Terraform Version') {
      steps {
        script {
          sh './terraformw'

          def requiredVersion = sh(script: 'cat version.tf | grep required_version | cut -d"=" -f2 | tr -d \'\"\' | tr -d \'\ ""', returnStatus: true).trim()
          def terraformVersion = sh(script: '.bin/terraform version -json | jq -r .terraform_version', returnStdout: true).trim()

           if (requiredVersion) {
            echo "Required Terraform version: ${requiredVersion}"

            if (terraformVersion == requiredVersion) {
                echo "Current Terraform version matches the required version."
            } else {
                error "Current Terraform version (${terraformVersion}) does not match the required version (${requiredVersion})."
            }
            } else {
            error "Unable to determine the required Terraform version"
          }
        }
      }
    }
    stage('Terraform Apply') {
      steps {
        sh './terraformw apply -auto-approve -no-color'
      }
    }
  }
  post {
    always {
      cleanWs()
    }
  }
}