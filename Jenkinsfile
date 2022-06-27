pipeline {
    agent {
    kubernetes {
      cloud 'ole-amr-linux'
      slaveConnectTimeout "600"
      yaml """
      kind: Pod
      apiVersion: v1
      spec:
        containers:
        # This JNLP container must be used, it has the SSL cert for both Jenkins contollers
        - name: jnlp
          image: amr-registry.caas.intel.com/ole-cicd/infra/jenkins-agent:jdk8-v1.1
          tty: true
          securityContext:
            runAsUser: 0
          imagePullPolicy: IfNotPresent
        # Change "your_OS" to your OS or however you've named your container
        - name: build-environment
          image: amr-registry.caas.intel.com/ipdk-cicd/test-images/p4-ubuntu20.04:1.0
          tty: true
          securityContext:
            runAsUser: 0
          resources:
            limits:
              memory: "24Gi"
              cpu: "7000m"
            requests:
              memory: "20Gi"
              cpu: "6500m"    
          imagePullPolicy: IfNotPresent
          command:
          - /bin/sh
          args:
          - -c
          - cat
      """
    }
  }
 options {
        buildDiscarder(logRotator(daysToKeepStr: '7', artifactDaysToKeepStr: '7'))
        skipDefaultCheckout() 
    }
 stages {
          stage('Clean workspace') {
            steps {
                cleanWs()
              }
            }
	  
	  stage ('clone test repo') {
		  steps {
			  script {
			  if (env.CHANGE_BRANCH) {
				CUR_BRANCH="${env.CHANGE_BRANCH}"
			  } else {
				CUR_BRANCH="${env.BRANCH_NAME}"
			   }
			  }
			checkout([$class: 'GitSCM', branches: [[name: "*/${CUR_BRANCH}"]], 
			doGenerateSubmoduleConfigurations: false, 
			extensions: [
		    		[$class: 'RelativeTargetDirectory', relativeTargetDir: "test"],
		    		[$class: 'CloneOption', depth: 0, noTags: true, shallow: true],
		    		[$class: 'SubmoduleOption', recursiveSubmodules: true, parentCredentials: true],
		    		[$class: 'CleanBeforeCheckout'],
		  	],
			submoduleCfg: [], 
			userRemoteConfigs: [[credentialsId: '0b6d7f05-0d2f-469e-9ef7-a983c8b1c876', 
			url: 'https://github.com/ipdksw/ipdk-gerrit-test.git']]]) 
	 	}
        
	  }
       stage ("Build DPDK code") { 
          steps {
		  withCredentials([usernamePassword(credentialsId: 'ipdksw_github_token', passwordVariable: 'github_pass', usernameVariable: 'github_user')]) {
              		sh '''
	      	 	echo "hello world!!!"
			'''
		 }		  
            }
       }
    }
}   
