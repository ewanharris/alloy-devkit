#! groovy
library 'pipeline-library'

def publishableBranches = ['master']
def nodeVersion = '10.15.0'
def yarnVersion = '1.16.0'

timestamps {
  node('(osx || linux) && git && npm-publish') {
    nodejs(nodeJSInstallationName: "node ${nodeVersion}") {
      ansiColor('xterm') {
        stage('Checkout') {
          checkout([
            $class: 'GitSCM',
            branches: scm.branches,
            extensions: scm.extensions + [[$class: 'WipeWorkspace'], [$class: 'LocalBranch', localBranch: "**"]],
            submoduleCfg: [],
            userRemoteConfigs: scm.userRemoteConfigs
          ])
          ensureYarn(yarnVersion)
        }
        stage('Prepare') {
          sh 'yarn'
          sh 'yarn lerna:bootstrap'
        }
        stage('Lint') {
          sh 'yarn lint'
        }
        stage('Test') {
          sh 'yarn test'
        }
        if(publishableBranches.contains(env.BRANCH_NAME)) {
          stage('Publish') {
            gitRemoteWithCredentials {
              sh 'yarn run lerna:publish'
            }
          }
        }
      }
    }
  }
}