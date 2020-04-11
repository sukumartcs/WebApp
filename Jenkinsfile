
node {
    // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
    def server = Artifactory.server "artifactory"
    // Create an Artifactory Maven instance.
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo
    
 rtMaven.tool = "maven"

    stage('Clone sources') {
        git url: 'https://github.com/sukumartcs/Webapp.git'
    }
    
	stage("build & SonarQube analysis") {     
              withSonarQubeEnv('sonarqube_Demo') {
		buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean sonar:sonar -Dsonar.host.url=http://18.222.254.192:9000 -Dsonar.login=admin -Dsonar.password=admin -Dsonar.inclusions=**/*.java -Dsonar.exclusions=src/test/java/servlet/*.java'

            }
          }
		  
	stage('Maven build') {
        buildInfo = rtMaven.run pom: 'pom.xml', goals: 'package -DskipTests'
    }

    stage('Artifactory configuration') {
        // Tool name from Jenkins configuration
        rtMaven.tool = "maven"
        // Set Artifactory repositories for dependencies resolution and artifacts deployment.
        rtMaven.deployer releaseRepo:'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
        rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server
    }

    stage('Publish build info') {
        server.publishBuildInfo buildInfo
    }
    }
	
