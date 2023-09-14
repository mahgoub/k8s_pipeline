version="1.0.0"
repository="ikolomiyets/demo-frontend"
tag="latest"
image="${repository}:${version}.${env.BUILD_NUMBER}"
namespace="demo"

podTemplate(label: 'demo-customer-pod', cloud: 'kubernetes', serviceAccount: 'jenkins',
  containers: [
    containerTemplate(name: 'ng', image: 'iktech/angular-client-slave', ttyEnabled: true, command: 'cat'),
    containerTemplate(name: 'docker', image: 'docker:dind', ttyEnabled: true, command: 'cat', privileged: true,
        envVars: [secretEnvVar(key: 'DOCKER_USERNAME', secretName: 'docker-hub-credentials', secretKey: 'username'),
    ]),
    containerTemplate(name: 'sonarqube', image: 'iktech/sonarqube-scanner', ttyEnabled: true, command: 'cat'),
    containerTemplate(name: 'kubectl', image: 'roffe/kubectl', ttyEnabled: true, command: 'cat'),
  ],
  volumes: [
    secretVolume(mountPath: '/etc/.ssh', secretName: 'ssh-home'),
    secretVolume(secretName: 'docker-hub-credentials', mountPath: '/etc/.secret'),
    hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')
  ]) {
    node('demo-customer-pod') {
        stage('Prepare') {
            checkout scm
        }

        stage('Build Docker Image') {
            container('docker') {
                sh """
                  docker build -t ${image} .
                  cat /etc/.secret/password | docker login --password-stdin --username $DOCKER_USERNAME
                  docker push ${image}
                  docker tag ${image} ${repository}:${tag}
                  docker push ${repository}:${tag}
                """
                milestone(1)
            }
        }
        stage('Deploy Latest') {
            container('kubectl') {
                sh "kubectl patch -n ${namespace} deployment demo-frontend -p '{\"spec\": { \"template\" : {\"spec\" : {\"containers\" : [{ \"name\" : \"demo-frontend\", \"image\" : \"${image}\"}]}}}}'"
                milestone(2)
            }
        }
    }
}

properties([[
    $class: 'BuildDiscarderProperty',
    strategy: [
        $class: 'LogRotator',
        artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10']
    ]
]);
