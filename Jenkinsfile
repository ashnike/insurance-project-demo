node{
        stage('git checkout'){
            echo "checking out the code from github"
            git 'https://github.com/ashnike/insurance-project-demo.git'
        }
        
        stage('maven build'){
            sh 'mvn clean package'
        }
        
        stage('build docker image'){
            sh 'docker build -t ashnike/insurance:1.0 .'
        }
        
        stage('push docker image to docker hub registry')
        {
            echo 'pushing images to registry'
            
            withCredentials([string(credentialsId: 'dockerpass', variable: 'dockerHubPassword')]) {
                sh "docker login -u ashnike -p ${dockerHubPassword}"
                sh 'docker push ashnike/insurance:1.0'
            }
        }
        
        stage('configure test-server and deploy insure-me'){
            echo "configuring test-server"
          //  sh 'ansible-playbook configure-test-server.yml'
            ansiblePlaybook become: true, credentialsId: 'ssh-key-ansibles', disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'configure-test-server.yml'
        }
        
}
