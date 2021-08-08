pipeline{
    agent any
    
    environment {
//         scannerHome = tool name: 'sonar_scanner_dotnet'
        dotnet ='C:\\Program Files (x86)\\dotnet\\'
        registry = 'shivamgupta04/samplewebapi'
        username = 'shivamgupta'
        registryCredential = 'DockerHub' 
        dockerImage = '' 
        masterAppPort = 7200
        developAppPort = 7300
        dockerPort = 7100
        masterAppName = 'dotnet-app-master-deployment'
        masterServiceName = 'dotnet-app-master'
        masterExposedPort = 30157
        developAppName = 'dotnet-app-develop-deployment'
        developServiceName = 'dotnet-app-develop'
        developExposedPort = 30158
        }
        
        stages{
 stage('Checkout') {
    steps {
         checkout scm
     }
  }
   stage('Nuget Restore'){
   steps{
       echo "Noget Restore step"
       bat "dotnet restore"
      }
   }
// stage('Start SonarQube Analysis') { 
// steps {
// echo "Start sonarQube Analysis Step"
// withSonarQubeEnv('Test_Sonar') {
// bat "${scannerHome}\\SonarScanner.MSBuild.exe begin /k:sonar_shivam /n:sonar_shivam /v:1.0"
// }
// }
// }
   stage('Code Build'){
   steps{
       echo "Clean previous build"
       bat "dotnet clean"
       echo "Code Build"
      // bat "dotnet build ${workspace}\\TestWebApi.csproj --configuration Release"
      bat "dotnet build -c Release -o DemoApi/app/build"

     }
  }
//  stage("Stop sonarQube Analysis") {
//         steps {
//           echo "Stop sonarQube analysis"
//           withSonarQubeEnv('Test_Sonar') {
//             bat "${scannerHome}\\SonarScanner.MSBuild.exe end"
//           }
//         }
//       }
stage('Docker Image'){
    steps{
        echo "Docker Image Step"
        bat "dotnet publish -c Release"
        // bat "docker build -t i_${username}_master --no-cache -f Dockerfile ."
        bat "docker build -t i_${username}_master:${BUILD_NUMBER} --no-cache -f DemoApi/Dockerfile ."
        // bat "docker build . -t i_${username}_master"
    }
}
            
//             stage('containers'){
//                 steps{
//                     parallel(
//                         "PrecontainerCheck" :{
//                             environment {
//                                 containerId="${bat(script: 'docker ps -aqf name=^i_shivamgupta_master$', returnStdout : true).trim().readLiness().drop(1)}"
//                             }
//                             when{
//                                 expression{
//                                     return containerId !== null
//                                 }
//                             }
//                             steps{
//                                 echo "stopping running container"
//                                 bat "docker stop i_${username}_${BRANCH_NAME} && docker rm i_${username}_${BRANCH_NAME}"
//                             }
//                         },
             

// "Move Image to Docker Hub" : {
//     steps{
//         echo "Move Image to Docker Hub"
//          bat "docker tag i_${username}_master ${registry}:${BUILD_NUMBER}"
//         withDockerRegistry([credentialsId: 'DockerHub', url: '']){
//             bat "docker push ${registry}:${BUILD_NUMBER}"
//         }
//     }
// }
//  )
// }
stage('Containers'){
    steps{
      parallel(
        'PreContainer Check':{
      script{
        def containerId = "${bat(returnStdout: true,script:'docker ps -aqf name=^i_shivamgupta_master$').trim().readLines().drop(1)}"
        if(containerId !='[]'){
           echo "${containerId}"
          echo "Deleting container if already running"
          bat "docker stop i_shivamgupta_master && docker rm i_shivamgupta_master"
        }
      }
        },
        'Push to Docker Hub':{
        script{
         echo "Move Image to Docker Hub"
          bat "docker tag i_${username}_master:${BUILd_NUMBER} ${registry}:${BUILd_NUMBER}"
          withDockerRegistry([credentialsId: 'DockerHub', url: ""]) {
            bat "docker push ${registry}:${BUILD_NUMBER}"
          }
      }
      })
    }
          }

stage('Docker Deployent'){
    steps{
        echo "Docker Deployment"
        bat "docker run --name i_${username}_master:${BUILD_NUMBER} -d -p 7100:80 ${registry}:${BUILD_NUMBER}"
       // bat "docker run --name TestApi -d -p 7100:80 i_${username}_master"
    }
}
 }
}

