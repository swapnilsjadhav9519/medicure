node {
    def mavenHome
    def mavenCMD
    def docker
    def dockerCMD
    def tagName
    
    stage('initialize'){
        echo 'Initialize the variables'
        mavenHome = tool name: 'myMaven' , type: 'maven'
        mavenCMD = "${mavenHome}/bin/mvn"
        docker = tool name: 'myDocker' , type: 'org.jenkinsci.plugins.docker.commons.tools.DockerTool'
        dockerCMD = "${docker}/bin/docker"
        
    }
    stage ('git code checkout'){
        
        echo 'pulling the code from github repo'
        git 'https://github.com/swapnilsjadhav9519/medicure.git'
        
   
    }
    stage('maven Build'){
        echo 'clean and compile and test package'
        //sh 'mvn clean package'
        sh "${mavenCMD} clean package"
    }
    stage('publish html reports'){
        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '/var/lib/jenkins/workspace/StrarAgileDevopsPipeline/target/surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report Staragile/var/lib/jenkins/workspace/StrarAgileDevopsPipeline', reportTitles: '', useWrapperFileDirectly: true])
    }
    
stage('Build the DockerImage of the application'){
       
        echo 'creating the docker image'
		// if you get permission denied issue
        //sudo usermod -a -G docker jenkins
        //restart Jenkins
        //or add sudoers file below line
        //jenkins ALL=(ALL) NOPASSWD:ALL
        sh "${dockerCMD} build -t swapnil9519/medicure:1.0 ."
        
       
    }
    stage('push the docker image to dockerhub'){
        echo 'pushing docker image'
        withCredentials([string(credentialsId: 'docker-password', variable: 'DockerPassword')]) {
        // some block
        sh "${dockerCMD} login -u swapnil9519 -p ${DockerPassword}"
        sh "${dockerCMD} push swapnil9519/medicure:1.0"
        }
    }
    stage('deploy the application'){
        
        ansiblePlaybook become: true, credentialsId: 'ansiblekey', disableHostKeyChecking: true, installation: 'MyAnsible', inventory: '/etc/ansible/hosts', playbook: 'ansible-playbook.yml'
    }
}
