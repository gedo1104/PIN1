pipeline {
  agent any

  options {
    timeout(time: 2, unit: 'MINUTES')
  }

  environment {
    ARTIFACT_ID = "mabelloa/webapp:${env.BUILD_NUMBER}"
  }
   stages {
   stage('Building image') {
      steps{
          sh '''
           docker build -t testapp .
             '''  
        }
    }
  
    // --rm elimina el contenedor después de ejecutar el comando
    stage('Run tests') {
      steps {
        sh "docker run --rm testapp npm test"
      }
    }
   stage('Deploy Image') {
      steps{
        sh '''
        docker tag testapp 127.0.0.1:5000/${ARTIFACT_ID}
        docker push 127.0.0.1:5000/${ARTIFACT_ID}   
        '''
        }
    }
    // Cleanup: elimina imágenes locales
    stage('Cleanup') {
      steps{
        sh '''
        # Eliminar la imagen local
        docker rmi testapp
        docker rmi 127.0.0.1:5000/${ARTIFACT_ID}
        '''
      }
    }
  }

  // Cleanup en caso de fallo también
  post {
    always {
      sh '''
      docker rmi -f testapp || true
      docker rmi -f 127.0.0.1:5000/${ARTIFACT_ID} || true
      '''
    }
  }
}
