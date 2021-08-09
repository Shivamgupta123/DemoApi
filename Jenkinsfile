pipeline{
    agent any
    
    environment {
         scannerHome = tool name: 'SonarQubeScanner'
        dotnet ='C:\\Program Files (x86)\\dotnet\\'
        registry = 'shivamgupta04/samplewebapi'
        username = 'shivamgupta04'
        registryCredential = 'DockerHub' 
        dockerImage = '' 
        cname = "c_${username}_develop"
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
stage('Start SonarQube Analysis') { 
steps {
echo "Start sonarQube Analysis Step"
withSonarQubeEnv('Test_Sonar') {
bat "${scannerHome}\\SonarScanner.MSBuild.exe begin /k:sonar_shivam /n:sonar_shivam /v:1.0"
}
}
}
   stage('Code Build'){
   steps{
       echo "Clean previous build"
       bat "dotnet clean"
       echo "Code Build"
      // bat "dotnet build ${workspace}\\TestWebApi.csproj --configuration Release"
      bat "dotnet build -c Release -o DemoApi/app/build"

     }
  }
 stage("Stop sonarQube Analysis") {
        steps {
          echo "Stop sonarQube analysis"
          withSonarQubeEnv('Test_Sonar') {
            bat "${scannerHome}\\SonarScanner.MSBuild.exe end"
          }
        }
      }
stage('Docker Image'){
    steps{
        echo "Docker Image Step"
        bat "dotnet publish -c Release"
        // bat "docker build -t i_${username}_master --no-cache -f Dockerfile ."
        bat "docker build -t i_${username}_develop:${BUILD_NUMBER} --no-cache -f DemoApi/Dockerfile ."
        // bat "docker build . -t i_${username}_master"
    }
}
            
stage('Containers'){
    steps{
      parallel(
        'PreContainer Check':{
      script{
        def containerId = "${bat(returnStdout: true,script:'docker ps -aqf name=^c_shivamgupta04_develop$').trim().readLines().drop(1)}"
        if(containerId !='[]'){
           echo "${containerId}"
          echo "Deleting container if already running"
            bat "docker stop ${cname} && docker rm ${cname}"
        }
      }
        },
        'Push to Docker Hub':{
        script{
         echo "Move Image to Docker Hub"
          bat "docker tag i_${username}_develop:${BUILD_NUMBER} ${registry}:${BUILD_NUMBER}"
          bat "docker tag i_${username}_develop:${BUILD_NUMBER} ${registry}:latest"
          withDockerRegistry([credentialsId: 'DockerHub', url: ""]) {
            bat "docker push ${registry}:${BUILD_NUMBER}"
            bat "docker push ${registry}:latest"
          }
      }
      })
    }
          }

stage('Docker Deployent'){
    steps{
        echo "Docker Deployment"
        bat "docker run --name ${cname} -d -p 7300:80 ${registry}:${BUILD_NUMBER}"
       // bat "docker run --name TestApi -d -p 7100:80 i_${username}_master"
    }
}
  stage('Kubernetes Deployment'){
    steps {
    echo "Deploying to kubernetes cluster"
    bat "kubectl apply -f deployment.yaml"
    }
  }
 }
}

