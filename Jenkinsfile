
podTemplate(
  name: 'build-pod', 
  label: 'build-pod',
  containers: [
      // containerTemplate(name: 'jnlp', image: 'jenkins/jnlp-slave:latest',args: '${computer.jnlpmac} ${computer.name}', workingDir: '/home/jenkins'),
      containerTemplate(name: 'docker', image:'docker:19.03.8', command: 'cat', ttyEnabled: true, workingDir: '/home/jenkins/agent'),
      containerTemplate(name: 'terraform', image: 'hashicorp/terraform:latest', ttyEnabled: true, command: 'cat', workingDir: '/home/jenkins/agent')
      
  ],
  volumes: [
      hostPathVolume(mountPath: '/var/run/docker.sock',hostPath: '/var/run/docker.sock')
      ],
  envVars: [
      envVar(key:'TF_VAR_region', value:'us-phoenix-1')
  ]
  
  ){
    //node = the pod label
    node('build-pod'){
      stage('Checkout') {
        checkout scm
      }
      //container = the container label
      stage('TF Plan') { 
        container('terraform') {
          sh 'terraform init'
          sh 'terraform plan -out myplan'
        }   
      }
      stage('Docker build') { 
        container('docker') {
          sh 'docker build -t docker-build:latest .'
         
        }   
      }
    //  stage('Approval') {
    //    script {
    //      def userInput = input(id: 'confirm', message: 'Apply Terraform?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Apply terraform', name: 'confirm'] ])
    //    }
    //  }
      stage('TF Apply') {
        container('terraform') {
          sh 'terraform apply -input=false myplan'
        }
      }
    }
  }

