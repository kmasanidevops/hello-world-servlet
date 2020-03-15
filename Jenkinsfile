pipeline {
   agent any

   tools {
      // Install the Maven version configured as "M3" and add it to the path.
      maven "M3"
   }

   stages {
      stage('Build') {
         steps {
            // Get code from a GitHub repository
            git 'https://github.com/kmasanidevops/hello-world-servlet.git'

            sh "mvn -Dmaven.test.failure.ignore=true clean package"
         }
      }
      stage('Scans: Master') {
         when { branch 'master' }
         steps {
            parallel(
               SonarQube: {
                  sh "mvn sonar:sonar"
                  echo "Getting the analysis results .. "
                  sh "/Users/kiran/anaconda3/bin/python3 /Users/kiran/parse_sonar.py --path $WORKSPACE/target/sonar/report-task.txt"
               },
               NexusLifeCycle: {
                  sh "echo 'hello world'"
               }
            )
         }
      }

      stage('Scans: Dev') {
         when { not { branch 'master' } }
         steps {
            sh "mvn sonar:sonar"
            echo "Getting the analysis results .. "
            sh "/usr/bin/python /opt/devops/scripts/parse_sonar.py --path $WORKSPACE/target/sonar/report-task.txt"
         }
      }

      stage('Nexus Repo') {
         steps {
            mvn deploy
         }
      }

      stage('Docker Build') {
         steps {
            sh "echo 'Running Docker build ..' "
         }
      }

      stage('Container Scan') {

         steps {
            sh "echo 'Running Container scan .. ' "
         }
      }

      stage('Deploy: DEV') {
         steps {
            sh "echo 'Deploying Docker ..' "
         }
      }      

   }
}
