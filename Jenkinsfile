pipeline {
    agent any
    tools {
        jdk 'myjava'
        maven 'mymaven'
    }
    stages {
        stage('Clone') {
            steps {
                echo "checking out the repo"
                git 'https://github.com/mrathne/pipelinecode.git'
            
            }
        }
        stage('build'){
            steps {
                echo "building the project"
                sh "cd MavenProject ; mvn clean install ; pwd"
            }
        }
        
        stage('Archieve Artifacts'){
            steps {
                echo "archiving the artifacts"
                archiveArtifacts 'MavenProject/multi3/target/*.war'
            }
            
        }
        stage('Deployment') {
            // Deployment
            steps {
                script {
                    echo "deployment"
                    sh 'chmod -R 755 MavenProject/multi3/target/'
                    sh 'cp MavenProject/multi3/target/*.war /home/centos/Tomcat8/apache-tomcat-8.0.9/webapps/'
                }
            }
        }
        stage('publish html report') {
            steps{
                echo "publishing the html report"
                publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: ''])
            }
        }
        stage('clean up') {
            steps {
                echo "cleaning up the workspace"
                cleanWs()
            }
        }
        stage("Metrics"){
            steps{
            parallel ( "JavaNcss Report":   
            { 
              node('window'){
                git 'https://github.com/mrathne/pipelinecode.git'
                sh "cd javancss-master ; mvn test javancss:report ; pwd"
                  }
            },
            "FindBugs Report" : {
            node('window'){
                sh "chmod -R 777 ../"
                sh "echo $workspace"
                sh "mkdir javancss1 ; cd javancss1 ;pwd"
                git 'https://github.com/mrathne/pipelinecode.git'
                sh "cd javancss-master ; mvn findbugs:findbugs ; pwd"
                deleteDir()
                }

              }
         )
            }
         post{
                success {
                    emailext body: 'Successfully completed pipeline project with archiving the artifacts', subject: 'Pipeline was successfull', to: 'jenkinsmodule4@gmail.com'
                }
    }
}
        
    }
}
