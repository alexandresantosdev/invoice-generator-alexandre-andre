pipeline{
    agent any
    stages {
      stage('Build/Deploy app to staging') {
        steps {
          echo 'Started Build/Deploy app to staging'
        }
      }

      stage('Run automated tests') {
        steps {
            echo 'Run automated tests'

        }
      }

      stage('Perform manual testing') {
        steps {
          echo 'Perform manual testing'
        }
      }

      stage('Release to production') {
        steps {
          echo 'Release to production'
        }
      }

    }
}