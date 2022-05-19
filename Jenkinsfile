pipeline {
    agent any

    tools { nodejs 'nodejs' }

    parameters {
    string(name: 'SPEC', defaultValue:'cypress/integration/1-getting-started/todo.spec.js', description: 'Enter the cypress script path that you want to execute')
    choice(name: 'BROWSER', choices:['electron', 'chrome', 'edge', 'firefox'], description: 'Select the browser to be used in your cypress tests')
    }

    stages {
      stage('Build/Deploy app to staging') {
        steps {
          sshPublisher(publishers: [sshPublisherDesc(configName: 'staging', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'cd .. && npm install && npm run', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**/*')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])
        }
      }

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
                  sh 'npx cypress run  --config-file cypress_pipeline.json --browser ${BROWSER} --spec ${SPEC} --reporter mochawesome'
                  sh 'npx mochawesome-merge cypress/results/*.json -o mochawesome-report/mochawesome.json'
                  sh 'npx marge mochawesome-report/mochawesome.json'
                }
            }
        }
        post {
        success {
          publishHTML (
                          target : [
                              allowMissing: false,
                              alwaysLinkToLastBuild: true,
                              keepAll: true,
                              reportDir: 'mochawesome-report',
                              reportFiles: 'mochawesome.html',
                              reportName: 'My Reports',
                              reportTitles: 'The Report'])
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
        timeout(activity: true, time: 5) { input 'Proceed to production?'
        }
        }
      }
    }
}
