pipeline {
    agent any
     environment {
       IMAGE = readMavenPom().getArtifactId()
       VERSION = readMavenPom().getVersion()
       VERSION_IMAGE="v${VERSION}"
       DOCKER_REGISTRY='romymendez'
       DOCKER_HUB_SECRETS=credentials('dockerhub')

    }
    stages {
	    stage('checkout github') {
			steps {
				echo 'Descargando codigo de SCM'
				sh 'rm -rf *'
				checkout scm
			}
		}
	    
	  stage('depenencias y versiones') {
	    steps {
                     checkout scm
                     echo '** Dependencias/ Versiones **'
		      def mvnHome = tool name: 'Apache Maven 3.6.0', type: 'maven'
                      sh "${mvnHome}/bin/mvn -B -DskipTests clean package"
		      sh "mvn -version"
			}
		}
		stage('Compilando Java') {
			steps {
            checkout scm
				echo '** Compilando Java **'
				sh 'mvn clean install -Dmaven.test.skip=true'
			}
		}
	
		stage('Construir la imagen docker') {
        
			steps {
            checkout scm
            echo("Contruir la version: ${VERSION_IMAGE} ")
				sh("docker build -t ${IMAGE}:${VERSION_IMAGE} .")
				sh("docker tag ${IMAGE}:${VERSION_IMAGE} ${DOCKER_REGISTRY}/${IMAGE}:${VERSION_IMAGE}")
			}
		}
        stage('docker deploy') {
		steps {
		checkout scm
                echo("Hago Docker Login")
                sh("docker login --username= ${DOCKER_REGISTRY} --password=${DOCKER_HUB_SECRETS}")
				sh("docker push ${DOCKER_REGISTRY}/${IMAGE}")
			}
		} 
	}
    
}
