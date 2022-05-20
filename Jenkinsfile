pipeline {
    agent {label 'ec2_private_slave'}
    environment {
        rds_hostname = 'terraform-20220519145155182200000004.c1vpvridguyo.us-east-1.rds.amazonaws.com'
        redis_hostname    = 'terra-redis-cluster.nvp9dd.0001.use1.cache.amazonaws.com:6379'
        }
    stages {
        stage('CI') {
            steps {
                // Build the Dockerfile
                sh "docker build . -t zainabsabry/jenkins_node_rds_redis:latest"
                // Push the image to our account

                //first login to docker
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]){

                    sh "docker login -u ${USERNAME} -p ${PASSWORD}"

                }

                //now, push the image
                sh "docker push zainabsabry/jenkins_node_rds_redis:latest"
                
            }
        }
        stage('CD') {
            steps {
            withCredentials([usernamePassword(credentialsId: 'rds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]){
                //Run the docker image with 
                sh "docker run -d -it -p 3000:3000 --env RDS_HOSTNAME=${rds_hostname} --env RDS_USERNAME=${USERNAME} --env RDS_PASSWORD=${PASSWORD} --env REDIS_HOSTNAME=${redis_hostname}  zainabsabry/jenkins_node_rds_redis:latest"
            }
                
            }
        }
    }
}
