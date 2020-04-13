//  Groovy Entrypoint for the Build Pipeline

def mainPipe
ansiColor('xterm') {
  node {
    println "\033[1;4;42m Checkout repo\033[0m"
    echo "Checkout repo"
    sh "rm -rf docker/pages/"
    checkout([
      $class: 'GitSCM',
      branches: scm.branches,
      doGenerateSubmoduleConfigurations: scm.doGenerateSubmoduleConfigurations,
      extensions: scm.extensions + [
        [$class: 'CloneOption', noTags: false, shallow: false, depth: 0],
        [$class: 'CleanBeforeCheckout']
      ],
      userRemoteConfigs: scm.userRemoteConfigs,
     ])

    println "\033[1;4;42m Checkout pipeline\033[0m"
    echo "Checkout pipeline"
    sh 'rm -rf pipeline'

    checkout([
      $class: 'GitSCM', branches: [[name: "master"]], doGenerateSubmoduleConfigurations: false,
      extensions: [
        [$class: 'RelativeTargetDirectory', relativeTargetDir: 'pipeline'],
        [$class: 'CleanBeforeCheckout']
      ],
      userRemoteConfigs: [[credentialsId: 'DeanPH-jenkins',
      url: 'https://github.com/DeanPH/jenkins_pipeline.git']]
    ])

    def trustedConfig = readTrusted "JenkinsConfig.json"
    writeFile file:'pipeline/JenkinsConfig.json', text:trustedConfig

    stash includes: '**', name: 'workspace'
    mainPipe = load ('pipeline/main.groovy')
    mainPipe.go()
  }
}
