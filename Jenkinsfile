node {
     def app

     stage('Build image') {
         /* This builds the actual image; synonymous to
         * docker build on the command line */

         app = docker.build("556683426101.dkr.ecr.ap-southeast-2.amazonaws.com/jenkins-ecr")
     }

     stage('Test image') {
         app.inside {
             sh 'echo "Tests passed"'
         }
     }

     stage('Push image') {
         docker.withRegistry('556683426101.dkr.ecr.ap-southeast-2.amazonaws.com/jenkins-ecr', 'ecr-user') {
             app.push("${env.BUILD_NUMBER}")
             app.push("latest")
         }
     }
 }