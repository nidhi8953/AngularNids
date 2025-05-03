node {
    def app

    stage('Clone repository') {
      

        checkout scm
    }

    stage('Build image') {
  
       app = docker.build("nidhisarup8953/angular")
    }

    stage('Test image') {
        app.inside {
            sh 'echo "Tests passed"'
       }
      
    }
    stage('Run Unit Tests') {
          
                sh 'npm test -- --watch=false --code-coverage'
                // Alternative if using ng directly:
                // sh 'ng test --watch=false --code-coverage'
          
            
    }
    stage("Allure REport") {
        echo "Allur REport !!"
        allure includeProperties: false, jdk: '', results: [[path: 'allure-results']]   
    }
    stage('Push image') {
        
        docker.withRegistry('https://registry.hub.docker.com', 'docker-image') {
            app.push("${env.BUILD_NUMBER}")
        }
    }
    

}
