node {
    stage('Configure') {
  //      env.PATH = "${tool 'maven-3.3.9'}/bin:${env.PATH}"
        version = '1.0.' + env.BUILD_NUMBER
        currentBuild.displayName = version

        properties([
                buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10')),
                [$class: 'GithubProjectProperty', displayName: '', projectUrlStr: 'https://github.com/aansari123/spring-boot-sample/'],
                pipelineTriggers([[$class: 'GitHubPushTrigger']])
            ])
    }

    stage('Checkout') {
        git 'https://github.com/aansari123/spring-boot-sample-master.git'
		
    }

    stage('Version') {
        echo "version=${version}"
		//bat 'set M2_HOME=E:\apache-maven-3.3.9'
		//bat 'set path=E:\apache-maven-3.3.9\bin:%path%;'
        //bat "mvn -B -V -U -e versions:set -DnewVersion=$version"
		//mvn -B -V -U -e versions:set -DnewVersion=$version
	    //bat mvn versions:set -DnewVersion=<version>
		//def mvnHome = tool 'Maven_3.3.9'
		//bat "${mvnHome}/bin/mvn -B verify"
		withEnv(["PATH+MAVEN=${tool 'Maven_3.3.9'}/bin"]) {
		bat 'mvn -B verify'
		}
    }

    stage('Build') {
        //mvn -B -V -U -e clean package
		bat 'mvn clean package'
    }

    stage('Archive') {
        junit allowEmptyResults: true, testResults: '**/target/**/TEST*.xml'
    }

    stage('Deploy') {
        // Depends on the 'Credentials Binding Plugin'
        // (https://wiki.jenkins-ci.org/display/JENKINS/Credentials+Binding+Plugin)
        withCredentials([[$class          : 'UsernamePasswordMultiBinding', credentialsId: 'cloudfoundry',
                          usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
            bat '''
                curl -L "https://cli.run.pivotal.io/stable?release=linux64-binary&source=github" | tar -zx

                ./cf api https://api.run.pivotal.io
                ./cf auth $USERNAME $PASSWORD
                ./cf target -o bertjan-demo -s development
                ./cf push
               '''
        }
    }
}