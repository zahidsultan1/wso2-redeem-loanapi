pipeline {
  environment {
     GATEWAY_FLAG = "TRUE"
  }

  agent {
    kubernetes {
      yamlFile 'builder.yaml'
    }
  }

  stages {
     stage('MVN') {
      steps {
        container('maven') {
          script {
            sh 'mvn -e clean install -Dmaven.test.skip=true'
          }
        }
      }
    }

    stage('IMG Build & Push') {
      steps {
        container('kaniko') {
          script {
            sh '''
            cd mi-config
            /kaniko/executor --cleanup --dockerfile `pwd`/Dockerfile \
                             --context `pwd` \
                             --destination=ghcr.io/zahidsultan1/wso2-prod//wso2/wso2mi/$JOB_NAME:$BUILD_NUMBER
            '''
          }
        }
      }
    }

    stage('TKG Deployment') {
      steps {
        container('kubectl') {
          withKubeCredentials(kubectlCredentials: [[caCertificate: '''MIICyzCCAbOgAwIBAgIBADANBgkqhkiG9w0BAQsFADAVMRMwEQYDVQQDEwprdWJl
            cm5ldGVzMB4XDTIyMDgwMzA5MzQyOVoXDTMyMDczMTA5MzkyOVowFTETMBEGA1UE
            AxMKa3ViZXJuZXRlczCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAM5a
            U28HQM5Gbdbb/r/GpkHeTW857o7Hxo19LSIerdoMzPaC0mTPktn4cEOgbE97BrEU
            f7Y9Cmt6o4a7B9HGlPO9ID7sZ3RBJFLwXNeHq1MJCMwGiOTn2C08Qtrf2pqGpovm
            VO96XyzgPCFqyNoklpiPsZ66iX/ajfAP0oXbP92mSqhVXFAE/DzWZQ792QjuKY3a
            AsjJXi/nYFRNToh0uAGcr70IbuoMygJZzUTW4kTXfsuaTAYQxBs2d2cTn4bUGDe0
            yOtQ2dAJLoTg/901OquQpgNLPBTNNpnm/aVNj6jOjpMS512XOw2RqgjBtLBUO1qP
            XJ0GFbS1bMhXMcaP+4MCAwEAAaMmMCQwDgYDVR0PAQH/BAQDAgKkMBIGA1UdEwEB
            /wQIMAYBAf8CAQAwDQYJKoZIhvcNAQELBQADggEBAG0isEO70igrP4v165MsRI73
            sA6D7ZFDZ9GSF9brsmeLsUID0AD33pU7HDD8KTsGvrg+ZE4I/kgZZHxKzjc2LoAj
            41VyL95wUwUQAQNZVPPjXc3Nfzow2FLG6J3BXFBg3VIMr3nuydhdLWIJz71T9JGK
            mDGeGn1hw0yR4vxC3P/PJYyWI8xp8wovr+323jIVBZAUNRz8rVAwK3cjfIqRumEi
            V6VoMMMvN0wUc1Mlyjvl//GTjVrndQ4KXLly11cNphHRQDwux21uzJ+hFa6LM5xg
            jNUrsA2GfXKitOevaBFGYVXqbY/pP2e0Hsn89JC7Tzf8AVCihyGE3dlo1beRWBs=''', 
            clusterName: '10.50.49.24', 
            contextName: 'tkc-prod-wso2-ns001', 
            credentialsId: 'jenkins-kubectl', 
            namespace: 'jenkins', serverUrl: 'https://10.50.49.24:6443']]) {
                  sh '''
                  	cd mi-config
                    sed -e "s/<WS02_APP_NAME>/\${JOB_NAME}/g" -e "s/<WS02_APP_TAG>/\${BUILD_NUMBER}/g" deployment.yaml > deployment-final.yaml
                    kubectl apply -f deployment-final.yaml
                  '''
              }
          }
        }
    }
    
    stage('Publish') {
      when {
	    expression {
	      return env.GATEWAY_FLAG == 'TRUE';
	    }
	  }
      steps {
        container('kaniko') {
          script {
            sh '''
            cd gw-config
            sed -e "s/<WS02_APP_NAME>/\${JOB_NAME}/g" -e "s/<WS02_APP_TAG>/\${BUILD_NUMBER}/g" deployment.toml > deployment-final.toml
            /kaniko/executor --cleanup --dockerfile `pwd`/Dockerfile \
                              --context `pwd` \
                              --destination=internal-registry.tkg.mobilink.net.pk/wso2-prod/wso2/wso2gw/$JOB_NAME:$BUILD_NUMBER
            '''
          }
        }
        container('kubectl') {
          withKubeCredentials(kubectlCredentials: [[caCertificate: '''MIICyzCCAbOgAwIBAgIBADANBgkqhkiG9w0BAQsFADAVMRMwEQYDVQQDEwprdWJl
            cm5ldGVzMB4XDTIyMDgwMzA5MzQyOVoXDTMyMDczMTA5MzkyOVowFTETMBEGA1UE
            AxMKa3ViZXJuZXRlczCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAM5a
            U28HQM5Gbdbb/r/GpkHeTW857o7Hxo19LSIerdoMzPaC0mTPktn4cEOgbE97BrEU
            f7Y9Cmt6o4a7B9HGlPO9ID7sZ3RBJFLwXNeHq1MJCMwGiOTn2C08Qtrf2pqGpovm
            VO96XyzgPCFqyNoklpiPsZ66iX/ajfAP0oXbP92mSqhVXFAE/DzWZQ792QjuKY3a
            AsjJXi/nYFRNToh0uAGcr70IbuoMygJZzUTW4kTXfsuaTAYQxBs2d2cTn4bUGDe0
            yOtQ2dAJLoTg/901OquQpgNLPBTNNpnm/aVNj6jOjpMS512XOw2RqgjBtLBUO1qP
            XJ0GFbS1bMhXMcaP+4MCAwEAAaMmMCQwDgYDVR0PAQH/BAQDAgKkMBIGA1UdEwEB
            /wQIMAYBAf8CAQAwDQYJKoZIhvcNAQELBQADggEBAG0isEO70igrP4v165MsRI73
            sA6D7ZFDZ9GSF9brsmeLsUID0AD33pU7HDD8KTsGvrg+ZE4I/kgZZHxKzjc2LoAj
            41VyL95wUwUQAQNZVPPjXc3Nfzow2FLG6J3BXFBg3VIMr3nuydhdLWIJz71T9JGK
            mDGeGn1hw0yR4vxC3P/PJYyWI8xp8wovr+323jIVBZAUNRz8rVAwK3cjfIqRumEi
            V6VoMMMvN0wUc1Mlyjvl//GTjVrndQ4KXLly11cNphHRQDwux21uzJ+hFa6LM5xg
            jNUrsA2GfXKitOevaBFGYVXqbY/pP2e0Hsn89JC7Tzf8AVCihyGE3dlo1beRWBs=''', 
            clusterName: '10.50.49.24', 
            contextName: 'tkc-prod-wso2-ns001', 
            credentialsId: 'jenkins-kubectl', 
            namespace: 'jenkins', serverUrl: 'https://10.50.49.24:6443']]) {
                  sh '''
                    cd gw-config
                    sed -e "s/<WS02_APP_NAME>/\${JOB_NAME}/g" -e "s/<WS02_APP_TAG>/\${BUILD_NUMBER}/g" deployment.yaml > deployment-final.yaml
                    kubectl apply -f deployment-final.yaml
                  '''
              }
        }
      //   container('apictl') {
      //     sh '''
      //     cd gw-config
      //     pwd
      //     ls -ltr
      //     /apictl/./apictl add env wso2-prod --apim https://wso2-cp-a-svc-ci.wso2.svc.cluster.local:9443
      //     /apictl/./apictl login wso2-prod -k -u admin -p admin
      //     /apictl/./apictl set --export-directory /home/jenkins/agent/workspace/wso2-redeem-loanapi/gw-config
		  // /apictl/./apictl import api --update -k -f JazzAdvanceLoan_v1.zip -e wso2-prod
		  // /apictl/./apictl import api --update -k -f JazzAdvanceLoan_v2.zip -e wso2-prod
      //     /apictl/./apictl import api --update -k -f Subscribe_loan_V1.zip -e wso2-prod
      //     /apictl/./apictl import app --update -k -f admin_Loyalty_Application.zip -e wso2-prod 
		  // '''

      //   }
      }
    }
  }
}
