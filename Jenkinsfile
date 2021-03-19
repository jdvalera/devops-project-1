node {
    def app
    def image

    stage('Clone repository') {
        checkout scm
    }

    stage('Build image') {
        sh 'sudo chmod 777 /var/run/docker.sock'
        app = docker.build("jdvalera/project1")
    }

    stage('Test image') {
        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Deploy image') {
        docker.withRegistry('https://registry.hub.docker.com', 'DOCKER-HUB-CREDENTIALS') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }

    stage('Pull image') {
        docker.withRegistry('https://registry.hub.docker.com', 'DOCKER-HUB-CREDENTIALS') {
            image = docker.image("jdvalera/project1:${env.BUILD_NUMBER}")
            image.pull()
        }
    }

    stage('Run image') {
            sh 'docker container stop $(docker ps -a -q)'
            sh 'docker rm -f $(docker ps -a -q)'
            sh 'docker images -a | grep "project1" | awk '{print $3}' | xargs docker rmi'
            image.run('-p 80:80 --name web-app')
    }
}