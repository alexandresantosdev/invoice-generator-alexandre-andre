pipeline {
    agent any

    tools { nodejs 'nodejs' }

    parameters {
    string(name: 'SPEC', defaultValue:'cypress/integration/generateInvoices.feature', description: 'Enter the cypress script path that you want to execute')
    choice(name: 'BROWSER', choices:['electron', 'chrome', 'edge', 'firefox'], description: 'Select the browser to be used in your cypress tests')
    }

    stages {
      stage('Build/Deploy app to staging') {
        steps {
          sshPublisher(publishers: [sshPublisherDesc(configName: 'staging', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '''cd /home/usr_2210617_my_ipleiria_pt/app/ 
          npm install 
          npm start''', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**/*')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
        }
      }
      parallel {
          stage('Run automated tests') {
            steps {
                dir ('/home/usr_2210617_my_ipleiria_pt/jenkins') {
                    sh 'rm -R qs_cypress/'
                    sh 'mkdir qs_cypress/'
                    sh 'chmod -R 777 qs_cypress/'
                    dir ('qs_cypress/') {
                      git 'https://github.com/andre00nogueira/software-quality-cypress.git'
                      sh 'npm prune'
                      sh 'npm cache clean --force'
                      sh 'npm i'
                      sh 'npm install npx'
                      sh 'npm install --save-dev mochawesome mochawesome-merge mochawesome-report-generator'
                      sh 'rm -f mochawesome.json'
                      sh 'npx cypress run  --config-file cypress_pipeline.json --reporter mochawesome'
                    }
                }
            }

          }
          stage('Static Analysis') {
            agent {
                label 'sonarqube'
            }
            steps {
                sh '<path_to_run_sonar-scanner'
            }
          }
      }

      stage('Perform manual testing') {
        steps {
          echo 'Perform manual testing'
        }
      }

      stage('Release to production') {
        steps {
                  sh 'rm -R node_modules/'
                  sshPublisher(publishers: [sshPublisherDesc(configName: 'production', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '''cd /home/usr_2210623_my_ipleiria_pt/app/
                  npm install
                  npm start''', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**/*')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])
                }
      }
    }
}
