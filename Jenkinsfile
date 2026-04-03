pipeline {
  agent any

  stages {
    stage('Prepare') {
      steps {
        sh 'echo "<h1>Hello from Jenkins</h1>" > index.html'
        sh 'ls -l'
      }
    }

    stage('Test') {
      steps {
        sh 'grep Hello index.html'
      }
    }

    stage('Package') {
      steps {
        sh 'tar -czf hello-ci.tar.gz index.html'
        sh 'ls -lh hello-ci.tar.gz'
      }
    }
  }
}
