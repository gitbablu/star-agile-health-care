pipeline {
  agent any
          
  stages {
    stage('Git Checkout') {
      steps {
        echo 'This stage is to clone the repo from github'
        git branch: 'master', url: 'https://github.com/gitbablu/star-agile-health-care.git'
                        }
            }
    stage('Create Package') {
      steps {
        echo 'This stage will compile, test, package my application'
        sh 'mvn package'
                          }
            }
    
    stage('Create Docker Image') {
      steps {
        echo 'This stage will Create a Docker image'
        sh 'docker build -t prembablu/healthcare:1.0 .'
                          }
            }
     stage('Docker-Login') {
           steps {
             withCredentials([usernamePassword(credentialsId: 'docker', passwordVariable: 'dockerpass', usernameVariable: 'dockeruser')]) {
               sh 'docker login -u ${dockeruser} -p ${dockerpass}'
                             
                        }
                }
}
    stage('Docker Push-Image') {
      steps {
        echo 'This stage will push my new image to the dockerhub'
        sh 'docker push prembablu/healthcare:1.0'
            }
      } 
    stage('AWS-Login') {
      steps {
       withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')])  {
         }
      }
    }
    stage('setting the Kubernetes Cluster') {
      steps {
        dir('terraform_files'){
          sh 'terraform init'
          sh 'terraform validate'
          sh 'terraform apply --auto-approve'
          sh 'sleep 20'
        }
      }
    }
    stage('deploy kubernetes'){
steps{
  sh 'sudo chmod 600 ./terraform_files/new.pem'    
  sh 'minikube start'
  sh 'sleep 30'
  sh 'sudo scp -o StrictHostKeyChecking=no -i ./terraform_files/new.pem deployment.yml ubuntu@172.31.26.65:/home/ubuntu/'
  sh 'sudo scp -o StrictHostKeyChecking=no -i ./terraform_files/new.pem service.yml ubuntu@172.31.26.65:/home/ubuntu/'
script{
  try{
  sh 'ssh -o StrictHostKeyChecking=no -i ./terraform_files/new.pem ubuntu@172.31.26.65 kubectl apply -f .'
  }catch(error)
  {
  sh 'ssh -o StrictHostKeyChecking=no -i ./terraform_files/new.pem ubuntu@172.31.26.65 kubectl apply -f .'
  }
}
}
}
  }
}

    
