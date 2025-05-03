node {
    def app
   // Use the NodeJS installation configured in Jenkins Global Tools
    def nodeJS = tool name: 'NodeJS 23.x', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
    
    // Add NodeJS to PATH
    env.PATH = "${nodeJS}/bin:${env.PATH}"

    // Set environment variables
    env.CHROME_BIN = '/usr/bin/google-chrome'
    env.DISPLAY = ':99' // Needed for some X11-based systems
        

        
    
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
    stage('Setup') {
            // Install Chrome and dependencies
            sh '''
            wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add -
            echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" > /etc/apt/sources.list.d/google-chrome.list
            apt-get update -qq
            apt-get install -y google-chrome-stable xvfb
            '''
    }
    stage('Run Unit Tests') {
          
            sh '''
            # Start virtual display
            Xvfb :99 -screen 0 1280x1024x24 &
            export DISPLAY=:99
            
            # Run tests with required Chrome flags
            npm test -- --watch=false --browsers=ChromeHeadless --no-sandbox
            '''
          
            
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
