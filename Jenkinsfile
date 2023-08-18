node("ci-node") {

  stage("checkout") {
    checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Samlogy/project-fs-devops-client']])
  }

  // stage("Quality Analyses"){
  //   sh "/opt/sonar-scanner/bin/sonar-scanner \\\n" +
  //     "  -Dsonar.projectKey=angular-s \\\n" +
  //     "  -Dsonar.sources=./src \\\n" +
  //     "  -Dsonar.projectName='angular-s \\\n" +
  //     "  -Dsonar.host.url=http://ci.check-consulting.net:11001 \\\n" +
  //     "  -Dsonar.token=sqp_a818bc464ceb4985bea7af89114471cde4b9842b"
  // }


  stage("build") {
    sh "npm install"
    sh "npm run build"
  }

  stage("build image") {
    sh "sudo docker build -t front-app ."
  }

  stage("push docker image") {
    withCredentials([usernamePassword(credentialsId: 's-docker-hub', usernameVariable: 'username',
      passwordVariable: 'password')]) {
      sh "sudo docker login -u '$username' -p '$password'"
      sh "sudo docker tag front-app sammmmmm/front-app:1.0"
      sh "sudo docker push sammmmmm/front-app:1.0"
      sh "sudo docker rmi sammmmmm/front-app:1.0"
      sh "sudo docker rmi front-app"
      // stash includes: 'docker-compose.yml', name: 'utils'
    }
  }

  stage("deploy-to-k8s") {
      withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: "aws-user"]]) {
          sh "aws eks update-kubeconfig --region us-east-2 --name sm-cluster"
          sh "helm upgrade sm-collab sm-collab-chart --install --create-namespace --namespace staff-manager"
      }
  }


}