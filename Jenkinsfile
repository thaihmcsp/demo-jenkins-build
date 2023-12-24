pipeline {
   agent { label 'Build-Server'}
   environment {
        ENV = "dev"
        NODE = "Build-server"
    }

   stages {
    stage('Build Image') {
        agent {
            node {
                label "Build-Server"
                customWorkspace "/home/demo-jenkin/build_dev_ops/"
                }
            }
        environment {
            // DOCKERHUB = credentials('thaihmcsp-docker')
            TAG = sh(returnStdout: true, script: "git rev-parse -short=10 HEAD | tail -n +2").trim()
        }
         steps {
            sh "ls"
            sh "docker build . -t build_server-$ENV:latest --build-arg BUILD_ENV=$ENV -f ./Dockerfile"


            sh "cat docker.txt | docker login -u thaihmcsp --password-stdin"
            // tag docker image
            sh "docker tag build_server-$ENV:latest thaihmcsp/build_server:$TAG"

            //push docker image to docker hub
            sh "docker push thaihmcsp/build_server:$TAG"

	    // remove docker image to reduce space on build server	
            sh "docker rmi -f thaihmcsp/build_server:$TAG"

           }
         
       }
	  stage ("Deploy ") {
	    agent {
        node {
            label "Target-Server"
                customWorkspace "/home/demo-jenkin/target/"
            }
        }
        environment {
            TAG = sh(returnStdout: true, script: "git rev-parse -short=10 HEAD | tail -n +2").trim()
        }
	steps {
            sh "sed -i 's/{tag}/$TAG/g' /home/demo-jenkin/target/docker-compose.yml"
            sh "docker compose up -d"
        }      
       }
   }
    
}
