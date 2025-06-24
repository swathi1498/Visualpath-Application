node{
   stage("CheckOutCode")
    {
        checkout([$class: 'GitSCM', branches: [[name: '*/docker-new']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/swathi1498/docker-practice.git']]])
    }
    
    stage("Build")
    {
        sh 'mvn install'
    }
    
   stage('Build Docker Image'){
     sh 'mkdir -p Docker-app/target'
     sh 'cp target/vprofile-v2.war Docker-app/target/'
     sh 'docker build -t kumarltd/vproappfix:$BUILD_ID Docker-app/'
     sh 'docker build -t kumarltd/vpronginx:$BUILD_ID Docker-web/'
     sh 'docker build -t kumarltd/vprodbfix:$BUILD_ID Docker-db/'
     sh 'docker tag kumarltd/vproappfix:$BUILD_ID kumarltd/vproappfix:latest'
     sh 'docker tag kumarltd/vpronginx:$BUILD_ID kumarltd/vpronginx:latest'
     sh 'docker tag kumarltd/vprodbfix:$BUILD_ID kumarltd/vprodbfix:latest'
   }
   
  stage('Push Docker Image'){ 
   withDockerRegistry(credentialsId: '9941d5ad-0f51-4929-aec4-abae7891ba8a', url: 'https://index.docker.io/v1/') {
    sh 'docker push kumarltd/vproappfix:latest'
    sh 'docker push kumarltd/vpronginx:latest'
    sh 'docker push kumarltd/vprodbfix:latest'
   }
 }
  stage('Deploy Docker Container into Docker Dev Server'){
     script {
     def dockerRun = 'docker run -p 8080:8080 -d --name vproapp kumarltd/vproappfix'
     def dockerRuns = 'docker run -p 80:80 -d --name vpronginx kumarltd/vpronginx'
     def dockerRunss = 'docker run -p 3306:3306 -d --name vprodbfix kumarltd/vprodbfix'

   sshagent(['0a8347e7-e99a-47cb-bf99-626cd74ee6a6']) {
    
    sh "scp -o StrictHostKeyChecking=no compose/* ubuntu@172.31.82.58:/home/ubuntu"
    sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.82.58 cd /home/ubuntu"
    sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.82.58 docker-compose up -d"
    }
     }
  }   
}
