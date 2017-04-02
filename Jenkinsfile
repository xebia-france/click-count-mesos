node("maven3") {
	checkout scm

	def version

	stage('Preparation') {
		sh 'git rev-parse HEAD > GIT_COMMIT'
		version = readFile('GIT_COMMIT').take(6)
	}

	stage('Build') {
		sh 'mvn clean package'
	}

	stage('Results') {
		archive 'target/clickCount.war'
	}

	stage('Build image') {
		sh "docker build -t registry.mesos.uo.techx.fr/xebiafrance/click-count:${version} ."
	}

	stage('Push image') {
		sh "docker push registry.mesos.uo.techx.fr/xebiafrance/click-count:${version}"
	}

	stage('Deploy on Staging') {
		sh "sed -i 's#{{.VERSION}}#${version}#' marathon.json"
		sh "curl -X PUT -H 'Content-type: application/json' http://mesos-master1.private:8080/v2/groups -d@marathon.json"
	}
}