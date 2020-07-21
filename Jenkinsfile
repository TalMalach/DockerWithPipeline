pipeline {
  agent { node { label 'tal-VirtualBox' } }

   stages {
      stage('Clone Sources') {
        steps {
          checkout scm
        } 
      }
	  stage('Checking environment') {
         steps {
            sh 'printenv'
         }
      }
	  stage('Prepare sources of the website') {
         steps {
	 sh '''
        cd docker
	    echo "Input param ${NAME}"		
	    echo "<p>Hello from ${NAME}!</p>" >> index.html
	 '''
         }
      }
	  
      stage('Build a docker image') {
         steps {
            echo 'Build process..'            
            sh '''
                cd docker
                docker build -t="mywebsite:${BUILD_NUMBER}" .
            '''
         }
      }
      stage('Push a docker image') {
         steps {
			sh '''
				docker tag mywebsite:${BUILD_NUMBER} ${REPO_NAME}/mywebsite:${BUILD_NUMBER}
				docker push ${REPO_NAME}/mywebsite:${BUILD_NUMBER}
			'''
         }
      }
      stage('Deploy the website') {
         steps {
            echo 'Deploy process..'
			sh '''
				echo "Stopping running containers"
				CONTAINER=`docker ps -q`
				docker stop ${CONTAINER}
				docker rm ${CONTAINER}
				echo "Running a new container"
				docker run -d -p 80:80 ${REPO_NAME}/mywebsite:${BUILD_NUMBER}
				echo "Check the URL: http://`hostname`:80"
			'''
         }
      }
      
   }
}
