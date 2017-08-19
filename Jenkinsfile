pipeline {
  agent {
    label 'vagrant'
  }
  stages {
    stage('Build') {
      steps {
        git 'https://github.com/zakeeruddin21/spring-music.git'
        wrap([$class: 'BuildUser']) {
          sh './gradlew clean build uploadArchives -PbuildNumber=${BUILD_NUMBER} -Pusername=${BUILD_USER_ID}'
        }
        // publishHTML(
        //   [allowMissing: true,
        //   alwaysLinkToLastBuild: true,
        //   keepAll: true,
        //   reportDir: 'build/reports/tests/test',
        //   reportFiles: 'index.html',
        //   reportName: 'UnitTestReport',
        //   reportTitles: ''])
      }
    }

    stage('Deploy') {
      steps {
        sh 'ssh vagrant@172.28.128.3 "rm -rf *.jar || true" "pkill -9 -f spring-music || true"'
        sh 'scp ./build/libs/spring-music.jar vagrant@172.28.128.3:/home/vagrant'
        sh 'ssh vagrant@172.28.128.3 "nohup java -jar /home/vagrant/spring-music.jar &"'
      }
    }
  }
}
