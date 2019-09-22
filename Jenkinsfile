node {
    def app
    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */
        checkout scm
    }
    
   stage ('Maven Build') {
 
    withMaven(
        // Maven installation declared in the Jenkins "Global Tool Configuration"
        maven: 'maven-3',
        // Maven settings.xml file defined with the Jenkins Config File Provider Plugin
        // We recommend to define Maven settings.xml globally at the folder level using
        // navigating to the folder configuration in the section "Pipeline Maven Configuration / Override global Maven configuration"
        // or globally to the entire master navigating to  "Manage Jenkins / Global Tools Configuration"
        mavenSettingsConfig: 'my-maven-settings') {
 
      // Run the maven build
      sh "mvn clean verify"
 
    } // withMaven will discover the generated Maven artifacts, JUnit Surefire & FailSafe & FindBugs & SpotBugs reports...
  }
    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */
        app = docker.build("dockerhubvasudev/sample-eureka-testing:${env.BUILD_NUMBER}")
    }
    stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */
        app.inside {
            sh 'echo "Tests passed"'
        }
    }
     stage('Push image') {
      /*  Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
            app.push("${env.BUILD_NUMBER}")
            
        }
    }
         /* stage('Run image') {
        Finally,Runt the image build. 
             docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
            app.run()
        }
                 
           }*/
}
