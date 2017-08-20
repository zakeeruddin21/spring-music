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
        sh 'ssh -o StrictHostKeyChecking=no -oBatchMode=yes vagrant@172.28.128.3 "rm -rf *.jar || true"'
        sh 'ssh vagrant@172.28.128.3 "nohup pkill -9 -f spring-music || true &"'
        // sh 'sleep 10'
        sh 'scp ./build/libs/spring-music.jar vagrant@172.28.128.3:/home/vagrant'
        sh 'ssh vagrant@172.28.128.3 "java -jar /home/vagrant/spring-music.jar > ./server.log &"'
        sh """
            ssh vagrant@172.28.128.3 'until fgrep -q "Tomcat started on port(s)" ./server.log;
            do sleep 3 && echo "."; done'"""
        sh 'ssh vagrant@172.28.128.3 "cat ./server.log"'
        echo 'Application is running at http://172.28.128.3:8090'
      }
    }
  }
}
