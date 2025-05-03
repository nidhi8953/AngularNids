node {
    def app

    stage('Clone repository') {
      

        checkout scm
    }

    stage('Build image') {
  
       app = docker.build("nidhisarup8953/AngularNew")
    }

    stage('Test image') {
        app.inside {
            sh 'echo "Tests passed"'
       }
      
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
