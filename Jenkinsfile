pipeline {
  agent none
  stages {
    stage('Cancel duplicates') {
      agent any
      steps {
        echo "Cancel any duplicates of ${env.JOB_NAME}"
        script {
          def cancelScript = load("/Users/electron/groovy/cancel-builds-same-job.groovy")
          cancelScript.cancelDuplicates()
        }
        echo "Done cancel any duplicates of ${env.JOB_NAME}"
      }
    }
    stage('Build') {
      parallel {
        stage('electron-osx-x64') {
            agent {
              label 'osx'
            }
            steps {
              timeout(60) {
                sh 'script/bootstrap.py --target_arch=x64 --dev'
                sh 'npm run lint'
                sh 'script/build.py -c D'
                sh 'script/test.py --ci --rebuild_native_modules'
              }
            }
            post {
              always {
                cleanWs()
              }
            }
        }
        stage('electron-mas-x64') {
          agent {
            label 'osx'
          }
          environment {
            MAS_BUILD = '1'
          }
          steps {
            timeout(60) {
              sh 'script/bootstrap.py --target_arch=x64 --dev'
              sh 'npm run lint'
              sh 'script/build.py -c D'
              sh 'script/test.py --ci --rebuild_native_modules'
            }
          }
          post {
            always {
              cleanWs()
            }
          }
        }
      }
    }
  }
}
