pipeline{
    agent any

    stages{
        stage('zip the file'){
            steps{
                sh '''
                    mkdir -p ansible-dev
                    rm -rf ansible-dev/*
                    shopt -s extglob
                    mv !(ansible-dev) ansible-dev/
                 '''
                dir ('/var/lib/jenkins/workspace/ansible-playbook/ansible-dev'){
                    sh 'rm -rf *.zip || echo ""'
                    sh 'zip -r ansible-${BUILD_ID}.zip * --exclude Jenkinsfile'
                }

                
            }
        }
        stage('upload artifacts to jfrog'){
            steps{
                sh 'curl -uadmin:AP9dALA6iGj7eV14S2pkgob7wRT -T \
                 ansible-dev/ansible-${BUILD_ID}.zip \
                "http://ec2-18-234-95-219.compute-1.amazonaws.com:8081/artifactory/ansible/ansible-${BUILD_ID}.zip"'
            }
        }
    stage('publish to ansible server'){
            steps{
                sshPublisher(publishers: [sshPublisherDesc(configName: 'Ansible_server', \
                transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: \
                'cd ansible-dev; unzip -o ansible-${BUILD_ID}.zip; rm -rf ansible-${BUILD_ID}.zip', execTimeout: 120000, flatten: false, makeEmptyDirs: false, \
                noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: \
                '.', remoteDirectorySDF: false, removePrefix: '', \
                sourceFiles: 'ansible-dev/ansible-${BUILD_ID}.zip')], usePromotionTimestamp: false, \
                useWorkspaceInPromotion: false, verbose: false)])
            }
        }   
    }
}
