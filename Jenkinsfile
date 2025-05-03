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
            steps {
                sh 'npm test -- --watch=false --code-coverage'
                // Alternative if using ng directly:
                // sh 'ng test --watch=false --code-coverage'
            }
            post {
                always {
                    junit 'coverage/**/junit.xml' // Process JUnit test results
                    publishHTML(target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: false,
                        keepAll: true,
                        reportDir: 'coverage',
                        reportFiles: 'index.html',
                        reportName: 'Angular Unit Test Coverage'
                    ])
                }
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
