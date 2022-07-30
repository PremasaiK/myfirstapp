node {
  def image
   stage ('checkout') {
        checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/PremasaiK/myfirstapp.git']]])      
        }
   
   stage ('Build') {
         def mvnHome = tool name: 'maven', type: 'maven'
         def mvnCMD = "${mvnHome}/bin/mvn "
         sh "${mvnCMD} clean package"           
        }
  
    stage('Build image') {
       app = docker.build("springboot")
    }

    stage('Push image to ecr') {
        docker.withRegistry('https://849654891981.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-east-1:PremasaiK') {
            app.push("${env.BUILD_NUMBER}")
        }
    }

    stage('K8S Deploy') {
          withKubeConfig([credentialsId: 'PremasaiK',
                    serverUrl: 'https://4D8697AE3FBC2C2C654CBA6F2328FE3E.gr7.us-east-1.eks.amazonaws.com',
                    contextName: 'arn:aws:eks:us-east-1:849654891981:cluster/master',
                    clusterName: 'arn:aws:eks:us-east-1:849654891981:cluster/master'
                    ]) {
         sh 'kubectl apply -f spring-boot.yaml'  
            }    
    }
}
