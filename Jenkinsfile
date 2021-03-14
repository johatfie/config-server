node {
    def mvnHome
    stage('Preparation') {
        // fetch the code
        git 'http://github.com/johatfie/config-server.git'
        mvnHome = tool 'M2_HOME'
    }
    stage('Build') {
        // Run the maven build
        withEnv(["MVN_HOME=$mvnHome"]) {
            if (isUnix()) {
                sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
            } else {
                bat(/"%MVN_HOME%\bin\mvn)" -Dmaven.test.failure.ignore clean package/)
            }
        }
    }
    stage('Results') {
        junit '**/target/surefire-reports/TEST-*.xml'
        archiveArtifacts 'target/*.jar'
    }
    stage('Build image') {
        sh "'${mvnHome}/bin/mvn' -Ddocker.image.prefix=326608040956.dkr.ecr.us-east-2.amazonaws.com/widgets-are-us -Dproject.artifactId=config-server -Ddocker.image.version=0.0.1-SNAPSHOT dockerfile:build"
    }
    stage('Push image') {
        docker.withRegistry('https://326608040956.dkr.ecr.us-east-2.amazonaws.com/widgets-are-us', 'ecr:us-east-2:ecr-user') {
            sh "docker push 326608040956.dkr.ecr.us-east-2.amazonaws.com/widgets-are-us/config-server:0.0.1-SNAPSHOT"
        }
    }
    stage('Kubernetes deploy') {
        // This plugin isn't working do to a bug handling the yaml in the deployfile.  I have not been able to find a suitable workaround.

        //kubernetesDeploy configs: 'config-server-deployment.yaml', kubeConfig: [path: ''], kubeconfigId: 'kubeconfig', secretName: '', ssh: [sshCredentialsId: '*', sshServer: ''], textDrecentials: [certificateAuthorityData: '', clientCertificateData: '', clientKeyData: '', serverUrl: 'https://84B31EEEAB9245573293B5C2AE2612D5.yl4.us-east-2.eks.amazonaws.com']
        //kubernetesDeploy configs: 'config-server-deployment.yaml', kubeConfig: [path: ''], kubeconfigId: 'kubeconfig', secretName: '', ssh: [sshCredentialsId: '*', sshServer: ''], textCredentials: [certificateAuthorityData: '', clientCertificateData: '', clientKeyData: '', serverUrl: 'https://']

        withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'kubeconfig file', namespace: '', serverUrl: '') {
            sh "env | sort"
            sh "sed -ie \"s/THIS_STRING_IS_REPLACED_DURING_BUILD/\$(date)/g\" config-server-deployment.yaml"
            sh "/usr/local/bin/kubectl apply -f config-server-deployment.yaml"
        }
    }

}
