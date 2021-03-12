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
        withKubeConfig([caCertificate: 'LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJeE1ETXdOakE0TURRME5Gb1hEVE14TURNd05EQTRNRFEwTkZvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTnh4CmpvcnUxUE1pRjhTZEI1aHdmVG1ZV1dsaEhQQnpzbk1zaEpVc010RCt3NlpmSTd2djlzS1dtVE9uaGhEY0VJeVgKc0d6MlV0V0EyYUJETjVucE5Xb0N1TzgyMzBzUy9NSHZCTmdKdktYSzZpOTlWUDhJVFFZS3hZRjRDbFFvaW9DNQpHUyswMzBkazdSSUlSYkgxckY4cENya05EN1hDK1RrSW5wdlVZYjQyMFA5UXJnbWZkSHR2Z2VOVDB3NVJkYzZsCnNwczRyUEl2MnVzQ2hwZldCUFJySjg3QzlvcFhQenRFTG95L0FUY2RrTEd2eStOd3NuVW9uR05JUzRwek9hd2EKc05qdG5YTDNtckc5bENPVWRBd085SXRzckxNQXQyY202OVdlOUZWSFMwK3p6YjFaYlpaanp0YzQ0TEpHdDhoSgpJM1BhRStwZzB4dDJzdkJ3NGprQ0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFLd3hscEVybytIcTk2SC9aanNvZWp6TEFjQS8Kdiswb2NaVHJHSllLeW5YblF3YVpvSTZZdXloMFhOdm9LdDNDSUJ5ZURzM1VBQUltcUhpQ0VHQ1k5V3NwZng0RApTRmhpVnYzNUNPQSs5UkFoZzNBK1FrVDhFV1lUaGtYS21QdG4yaHBRa21ZVDFkQU1aMjYzeVkvZXZXMFo0YnlUCm9HdEVjQWJGRzNKRDRQUHZJMXNFdlJndG1neFlBSlgrQURNaTk0MWhPbk5IWFlhWW1GZnIwME1PVnMra1NhUkwKQno0ZjFyNnl5TmEySDZsd3FxTnJhaTMyZ0xYLzdWQXA5UFpvbHBEWGlFdU56bnBEVmUzZXZId24zK2RNVUVGRwoxLzZWbXZ5cm5CMEFmN0JSbndxMEJvb3poL0RTeFJ3aFFSb0ZVVGdhRFZhcnRnaUhTMDh3bEQybGFrOD0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=',
                clusterName: 'arn:aws:eks:us-east-2:326608040956:cluster/widgets-are-us',
                contextName: 'arn:aws:eks:us-east-2:326608040956:cluster/widgets-are-us',
                credentialsId: 'kubeconfig',
                namespace: '',
                serverUrl: 'https://84B31EEEAB9245573293B5C2AE2612D5.yl4.us-east-2.eks.amazonaws.com']) {

            // some block
            //sh "/usr/local/bin/kubectl get pods | grep config-server | awk '{ print \$1 }' | xargs /usr/local/bin/kubectl delete pod"
            //sh "sed -ie \"s/THIS_STRING_IS_REPLACED_DURING_BUILD/\$(date)/g\" /var/lib/jenkins/workspace/config-server/config-server-deployment.yml"
            sh "env | sort"
            //sh "cat /home/ec2-user/.kube/config"
            //sh "cat /var/lib/jenkins/.kube/config"

            sh "sed -ie \"s/THIS_STRING_IS_REPLACED_DURING_BUILD/\$(date)/g\" config-server-deployment.yaml"
            //sh "/usr/local/bin/kubectl apply -f /var/lib/jenkins/workspace/config-server/config-server-deployment.yml"
            //sh "/usr/local/bin/kubectl --kubeconfig=/home/ec2-user/.kube/config apply -f config-server-deployment.yaml"
            sh "/usr/local/bin/kubectl apply -f config-server-deployment.yaml"
        }
    }

}
