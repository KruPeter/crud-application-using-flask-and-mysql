node("linux"){
  def app
  
  stage("Get Repo from brach") {
    git branch: 'master',
    url: 'https://github.com/KruPeter/crud-application-using-flask-and-mysql.git' 
    checkout scm
  }
  
  stage("Create Docker Image") {
    app = sh(script: 'docker build -q -f Dockerfile -t peterkr/project_app .', returnStdout: true)
    mysql = sh(script: 'docker build -q -f Dockerfile-mysql -t peterkr/project_app_mysql .', returnStdout: true)
  }
  
  stage('docker-compose') {
    sh '''
    docker-compose up -d
    '''
  }
  
   stage('Test container') {
       response = sh(script: "sleep 10; curl 127.0.0.1:8181", returnStdout: true)
  }  
  
  stage('docker-compose') {
    sh '''
    docker-compose down
    '''
  }
  
  stage("Push to DockerHub") {
    withDockerRegistry(registry:[
        url: '',
        credentialsId: 'dockerhub.peter.krumer'
        ]){
    sh(script: 'docker push peterkr/project_app', returnStdout: true)
    sh(script: 'docker push peterkr/project_app_mysql', returnStdout: true)
  }
    
  }
  stage('Slack it'){
    slackSend color: "#439FE0", message: "Build Started: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
  }
}
