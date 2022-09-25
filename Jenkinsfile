node{
    def mavenHome
    def mavenCMD
    def docker
    def dockerCMD
    
    stage('preparation for jenkins'){
        echo 'initializing variable and tools'
        mavenHome = tool name:'maven' , type: 'maven'
        mavenCMD = "${mavenHome}/bin/mvn"
	    docker = tool name: 'docker', type: 'org.jenkinsci.plugins.docker.commons.tools.DockerTool'
	    dockerCMD = "${docker}/bin/docker"
        
    }
    
    stage('git checkout'){
        try{
        echo 'checking out code from git repository'
        git 'https://github.com/MayuriHivare/Project-2.git'
    }
    catch(Exception e){
        echo "Exception occured in checking out the code from git repository"
        currentBuild.result="FAILURE"
        emailext body: '''Hi Team,
        The build has been failed. Request you to have a look and fix it asap.''', subject: 'Jon ${JOB_NAME} ($BUILD_NUMBER} is failed', to: 'mayurilade1994@gmail.com'
    }
    }
    
    
    
    stage('clean package'){
       sh 'mvn clean package'
       sh "${mavenCMD} clean package"
    }
    
    stage('generating test reports execute code quality and security analysis'){
        echo 'generating test reports'
    }
    
    stage('Building docker image'){
        echo 'Building docker image from docker file...'
        sh "${dockerCMD} build -t mayurihivare/addressbook:8.0 ."
    }
    
    stage('Login and push to Dockerhub'){
        echo "loggings in to Dockerhub and pushing the image"
        withCredentials([string(credentialsId: 'dockerHubPwd', variable: 'dockerHub')]) {
    
            sh "${dockerCMD} login -u mayurihivare -p ${dockerHub}"
            echo "pushing the image to the dockerHub......"
            sh "${dockerCMD} push mayurihivare/addressbook:8.0"
            
    
}
        
        
    }
    
    stage('configure test server and deploy'){
        echo "configuring test server and deploy"
        ansiblePlaybook credentialsId: 'private-key0', disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'configure-server.yml'
    }
    
    stage('configure and deploy to production server'){
        echo 'deploying to production server'
    }
    
    stage('clear workspace'){
        echo 'cleaning up workspace'
        cleanWs()
    }
    
    
    
}
