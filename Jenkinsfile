node {
    stage('Checkout') {
    	checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'b0236129-b8f4-4e21-8827-3083abafa57b', url: 'https://github.com/jchawla2018/ttn-drill-asst1.git']]])
    }

    stage('Build') {
    	sh 'mvn clean install'
    }

	stage('Build Docker Image'){
		docker.withServer('tcp://127.0.0.1:2375'){
		docker.build("jitinchawla/application:${BUILD_NUMBER}")
	}
	}
	stage('Push Docker Image') {
	    docker.withServer('tcp://127.0.0.1:2375'){
	    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'b0236129-b8f4-4e21-8827-3083abafa57b',
usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]){
		sh 'docker login -u $USERNAME -p $PASSWORD ; docker push jitinchawla/application:${BUILD_NUMBER}'
	}	}	}

	stage('Deploy'){
		sshPublisher(publishers: [sshPublisherDesc(configName: 'docker', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'docker run -itd -P jitinchawla/application:${BUILD_NUMBER}', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
	}
}