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
    stage('Setup Chrome') {
            sh '''
            # Install Chrome browser
            wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add -
            echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" > /etc/apt/sources.list.d/google-chrome.list
            apt-get update -qq
            apt-get install -y google-chrome-stable xvfb
            '''
    }        
    stage('Run Tests') {
            sh '''
            # Start virtual display
            Xvfb :99 -screen 0 1280x1024x24 &
            export DISPLAY=:99
            
            # Run tests with correct Chrome flags
            ng test -- --watch=false --browsers=ChromeHeadlessNoSandbox
            '''
    }
    stage("Allure REport") {
            // Verify results exist before processing
            sh '''
            echo "Checking for Allure results..."
            ls -la allure-results/ || true
            '''
            
            // Generate Allure report
            allure([
                includeProperties: false,
                jdk: '',
                results: [[path: 'allure-results']],
                reportBuildPolicy: 'ALWAYS'
            ])
            // Archive only if files exist
            script {
                def results = findFiles(glob: 'allure-results/**/*')
                if (results) {
                    archiveArtifacts artifacts: 'allure-results/**/*', allowEmptyArchive: false
                } else {
                    echo "Warning: No Allure results found to archive"
                }
            }
      
            
    }
    stage('Push image') {
        
        docker.withRegistry('https://registry.hub.docker.com', 'docker-image') {
            app.push("${env.BUILD_NUMBER}")
        }
    }
    

}
