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
            app.push("latest")
        }
    } 

  stage('K8S Deploy')  {
         script {
                withKubeConfig([credentialsId: 'kubeconfig', serverUrl: '']) {
                sh ('kubectl apply -f  spring-boot.yaml')
                }
            }
      }
   }
