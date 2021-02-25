pipeline {
     agent {
    docker {
      image 'maven:3.6.3-jdk-11'
      args '-v /root/.m2:/root/.m2'
    }
  }
    stages{
          stage('Fetch') {
            steps {
               sh 'echo $JOB_NAME'
                sh 'echo $BUILD_NUMBER'
                sh 'echo Fetch'
               git branch: 'master', url: 'https://github.com/roshenreji/SpringChallenge.git'
            }
        }
        
        stage('Clean'){
            steps{
                dir("/var/jenkins_home/workspace/pipeline-aws"){
                    sh 'echo Clean'
                    sh 'mvn  clean'
                }
            }
        }
        stage('Validate'){
                steps{
                     dir("/var/jenkins_home/workspace/pipeline-aws"){
                
                        sh 'mvn  validate'
                     }
                }
            
         }
        stage('Compile'){
                steps{
                     dir("/var/jenkins_home/workspace/pipeline-aws"){
                 
                        sh 'echo Compile'
                         sh 'mvn  compile'
                     }
                }
            
        }
        
        
             stage('Test'){
                 steps {
                      dir("/var/jenkins_home/workspace/pipeline-aws"){
                         sh 'echo Test'
                         sh 'mvn test'
                      }
                 }
                 post{
                     always {
                         junit '**/target/surefire-reports/TEST-*.xml'
                     }
                 }
            }

            stage('Sonar Analysis'){
            steps{
                 dir("/var/jenkins_home/workspace/pipeline-aws"){
                    withSonarQubeEnv('Sonar'){
                        withMaven(maven:'maven'){
                            sh 'mvn sonar:sonar'
                        }
                        
                  }
                }
            }
            
        }

        stage('SonarQube Quality Gate') { 
            steps{
                timeout(time: 1, unit: 'HOURS') { 
                    script{
                        def qg = waitForQualityGate() 
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                         }
                    }
                    
                }
            }
        }
            stage('Build'){
            steps {
                 dir("/var/jenkins_home/workspace/pipeline-aws"){
                
                        sh 'echo Build'
                        sh 'mvn  package -Dbuild.number=-${BUILD_NUMBER}'
                 }
            }
            
            post {
                always {
                    //junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'CodingChallenge-2/target/*.jar'
                     }
                }
             }

             
            stage('collect artifact'){
                steps{
                    archiveArtifacts artifacts: 'pipeline-aws/target/*.jar', followSymlinks: false
                 }
            }      
        /* stage('deploy to artifactory')
         {
            steps{
     
                rtUpload (
                    serverId: 'JfrogId',
                    spec: '''{
                    "files": [
                         {
                             "pattern": "CodingChallenge-2/target/*.jar",
                             "target": "art-doc-dev-loc-new/sample/"
                        }
                     ]
                }''',
 
  
    
)
     }}
     stage('download from artifactory')
         {
            steps{
     
                
     }}*/
     
     

    /* stage('Deliver') {
        steps {
            sshagent(['4baffbbd-61c4-4561-b27c-021e3f5f66ef']){
            sh 'ssh -i "newpem.pem" ubuntu@ec2-54-201-166-106.us-west-2.compute.amazonaws.com pwd'
           sh 'scp -v -o StrictHostKeyChecking=no  -i C:/Users/roshe/.jenkins/workspace/AwsChallenge/CodingChallenge-2/target/*.jar ubuntu@54.201.166.106:/home/ubuntu'
            // sh "sshpass -p password ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/secrets/mykey ubuntu@00.00.00.00 '/home/ubuntu/start.sh'"
            }
        
    }
    }*/
    }
    post{
        success{
            echo 'I succeeded!'
    
             rtUpload (
                    serverId: 'JfrogId',
                    spec: '''{
                    "files": [
                         {
                             "pattern": "pipeline-aws/target/*.jar",
                             "target": "art-doc-dev-loc"
                        }
                     ]
                }''',
 
  
    
)


rtDownload (
                    serverId: 'JfrogId',
                    spec: '''{
                    "files": [
                         {
                             "pattern": "art-doc-dev-loc",
                             "target": "pipeline-aws/"
                        }
                     ]
                }''',
 
  
    buildName: 'Build2',
    buildNumber: '1'
)

       sshagent(['4caf8f9d-4507-4358-a814-4a2866505100']){
                    sh 'scp -r /var/jenkins_home/workspace/pipeline-aws/target/*.jar ubuntu@18.216.159.12:/home/ubuntu/artifacts'
        }
        }
        failure{
            echo 'I failed!'
           
        }

    }
}
