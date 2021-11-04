pipeline {
  agent {
    kubernetes {
      label 'promo-app'  // all your pods will be named with this prefix, followed by a unique id
      idleMinutes 5  // how long the pod will live after no jobs have run on it
      yamlFile 'build-pod.yaml'  // path to the pod definition relative to the root of our project 
      defaultContainer 'maven'  // define a default container if more than a few stages use it, will default to jnlp container
    }
  }
  stages {
    stage('Build') {
      steps {  // no container directive is needed as the maven container is the default
        sh "mvn clean package"   
      }
    }
    stage('Build Docker Image') {
      steps {
        container('docker') {  
          sh "buildah bud --tls-verify=false --storage-driver=vfs --layers -f Dockerfile -t demo/test-pipeline:dev ."
          sh "buildah push --storage-driver=vfs --tls-verify=false demo/test-pipeline:dev docker://registry.k8s.example.com/demo/test-pipeline:dev"
        }
      }
    }
  }
}
